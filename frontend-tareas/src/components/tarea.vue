<template>
    <div>
        <h1 class="display-4 text-center">Listado de tareas</h1>
        <hr>
        <div class="row">
            <div class="col-lg-8 offset-lg-2">
                <div class="card mt-4">  
                    <div class="card-body">
                        <div class="input-group input-group-lg">
                            <input v-model="tarea" class="form-control" placeholder="Agregar tarea">
                            <div class="input-group-append">
                                <button v-on:click="agregarTarea()" class="btn btn-success btn-lg">Agregar</button>
                            </div>                            
                        </div>
                            <br>
                            <div class="text-center">
                                <div v-if="loading"    class="spinner-grow text-success" role="status">
                                    <span class="visually-hidden">Loading...</span>
                                </div>
                            </div>
                            <h5 v-if="listareas.length == 0" class="text-center">No hay tareas para realizar</h5>
                            <ul v-if="!loading" class="list-group">
                                <li v-for="(tarea,index) of listareas" :key="index"
                                class="list-group-item d-flex justify-content-between">
                                    <span class="cursor" v-bind:class="{'text-success':tarea.estado}" v-on:click="editarTarea(tarea,tarea.id)">
                                        <i v-bind:class="[tarea.estado ? 'fas fa-check-circle' :'far fa-circle']"></i>
                                        
                                    </span>
                                    {{tarea.nombre}}
                                <span class="text-danger cursor" v-on:click="eliminarTarea(tarea.id)">
                                    <i class="fas fa-trash-alt"></i>
                                </span>
                                </li>
                            </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
const URL = "https://backend-sevices-task.azurewebsites.net/api/tarea/";
    export default {
        name:'Tarea',
        data(){
            return {

                tarea: '',
                listareas:[],
                loading:false
            }
        },
        methods:{
            agregarTarea(){
                const tarea={
                    nombre:this.tarea,
                    status:false
                }
                //this.listareas.push(tarea);
                this.loading=true;
                axios.post(URL,tarea).then(response=>{
                    console.log(response);
                    this.obtenerTareas();
                    this.loading=false;
                }).catch(error=>{
                    console.error(error),
                    this.loading=false;
                    });
                this.tarea='';
            },
            eliminarTarea(id){
                //this.listareas.splice(index,1)
                axios.delete(URL + id).then(response=>{
                    console.log(response);
                    this.obtenerTareas();
                }).catch(error=>console.log(error));
            },
            editarTarea(tarea,id){
                //this.listareas[index].estado =! tarea.estado;
                this.loading=true;
                axios.put(URL+id,tarea).then(()=>{
                    this.obtenerTareas(),
                    this.loading=false
                }).catch(error=>{
                    console.error(error),
                    this.loading=false;
                    });
            },

            obtenerTareas(){
                axios.get(URL).then(response=>
                {
                    console.log(response);
                    this.listareas = response.data;
                })
            }
        },

        created:function(){
            this.obtenerTareas();
        }
        
    }
</script>

<style  scoped>
.cursor{
    cursor: pointer;
}

</style>