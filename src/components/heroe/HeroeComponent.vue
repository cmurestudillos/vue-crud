<template>
    <div>
        <h1 v-if="heroe.nombre">Heroe: <small>{{heroe.nombre}}</small> </h1>
        <h1 v-if="!heroe.nombre">Heroe: <small>Nombre del heroe</small> </h1>
        <hr>

        <div class="row text-right">
            <div class="col">
                <router-link :to="'/heroes'" class="btn btn-outline-primary" title="Volver"><font-awesome-icon icon="arrow-left"/> Volver </router-link>
            </div>
        </div>     
        <div class="row">
            <div class="col">
                <form @submit.prevent="guardar">
                    <div class="form-group">
                        <label>Id Firebase</label>
                        <input type="text" class="form-control" placeholder="Id Firebase" v-model="heroe.id" name="id" disabled>
                        <small class="form-text text-muted">Este campo se genera automaticamente.</small>
                    </div>
                    <div class="form-group">
                        <label>Nombre</label>
                        <input type="text" class="form-control" placeholder="Nombre" v-model="heroe.nombre" name="nombre" required>
                    </div>
                    <div class="form-group">
                        <label>Poder</label>
                        <input type="text" class="form-control" placeholder="Poder" v-model="heroe.poder" name="poder">
                    </div>
                    <div class="form-group">
                        <label>Estado</label>
                        <br>
                        <button v-if="heroe.estado" @click="heroe.estado = false" class="btn btn-outline-success w-25" type="button" title="Vivo"><font-awesome-icon icon="thumbs-up" title="Vivo" /> Vivo </button>
                        <button v-if="!heroe.estado" @click="heroe.estado = true" class="btn btn-outline-danger w-25" type="button" title="Muerto"><font-awesome-icon icon="thumbs-down" title="Muerto" /> Muerto </button>
                    </div>
                    <hr>
                    <div class="form-group text-center">
                        <button type="submit" class="btn btn-outline-primary w-25" title="Guardar"><font-awesome-icon icon="save" title="Guardar" /> Guardar </button>
                    </div>
                </form>
            </div>
        </div>           
    </div>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';
import HeroeModel from '../../models/Heroe.js';

export default {
    name: 'HeroeComponent',
    mounted(){
        this.heroeId = this.$route.params.id; 
        if(this.heroeId !== 'nuevo'){
            this.getHeroeById(this.heroeId);
        }
    }, 
    data(){
        return {
            api: 'https://crud-frameworks-default-rtdb.europe-west1.firebasedatabase.app',
            heroe: new HeroeModel('', '', '', '')
        }
    },  
    methods: {
        getHeroeById(heroeId){
            axios.get(this.api + '/heroes/' + heroeId + '.json')
            .then( res => {
                if(res.data){
                    this.heroe = res.data;
                    this.heroe.id = this.heroeId;
                }
            });
        },        
        guardar(){
            if(this.heroeId !== 'nuevo'){
                this.heroe.id = this.heroeId;
                axios.put(this.api + '/heroes/' + this.heroeId + '.json', this.heroe)
                .then( res => {
                    if(res.data){
                        Swal.fire({
                            position: "top-end",
                            icon: "success",
                            title: "El Heroe ha sido modificado correctamente.",
                            showConfirmButton: false,
                            timer: 1500
                        });                           
                        this.$router.push('/heroes');  
                    }
                })
                .catch(err => {
                    console.log(err); 
                });
            }else{
                axios.post(this.api + '/heroes.json', this.heroe)
                    .then( res => {
                    if(res.data){
                        Swal.fire({
                            position: "top-end",
                            icon: "success",
                            title: "El Heroe ha sido creado correctamente.",
                            showConfirmButton: false,
                            timer: 1500
                        });                         
                        this.$router.push('/heroes');                    
                    }
                })
                .catch(err => {
                    console.log(err); 
                }); 
            }
        }
    }         
}
</script>