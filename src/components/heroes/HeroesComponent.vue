<template>
    <div>
        <h1>Listado de Heroes</h1>
        <hr>
        <div class="row">
            <div class="col text-right">
                <router-link :to="'/heroe/nuevo'" class="btn btn-outline-primary" title="Alta"><font-awesome-icon icon="plus"/> Nuevo </router-link>
            </div>
        </div>  

        <table aria-describedby="tabla-heroes" v-if="!cargando" class="table mt-3">
            <thead class="bg-custom">
                <tr>
                    <th scope="col">Nombre</th>
                    <th scope="col">Poder</th>
                    <th scope="col">Estado</th>
                    <th scope="col" colspan="2" class="text-center">Opciones</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for=" heroe in heroes" :key="heroe.id">
                    <td>{{ heroe.nombre}}</td>
                    <td>{{ heroe.poder}}</td>
                    <td>
                        <font-awesome-icon v-if="heroe.estado" icon="thumbs-up" class="text-success fa-2x" title="Vivo" />
                        <font-awesome-icon v-if="!heroe.estado" icon="thumbs-down" class="text-danger fa-2x" title="Muerto" />
                    </td>
                    <td class="text-center">
                        <router-link :to="'/heroe/'+ heroe.id" class="btn btn-outline-warning mr-1" title="Modificar"><font-awesome-icon icon="edit"/></router-link>
                        <button class="btn btn-outline-danger" @click="borrarHeroe(heroe.id)" title="Eliminar"><font-awesome-icon icon="trash"/></button>
                    </td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <td colspan="5" class="bg-custom"><span><e>Copyright© - Carlos Mur</e></span></td>
                </tr>
            </tfoot>
        </table> 

        <div v-if="!cargando && !heroes" class="alert alert-warning text-center mt-3">
            <h4 class="alert-heading">No hay registros</h4>
            <p><i class="fa fa-exclamation fa-2x"></i></p>
        </div>

        <div v-if="cargando" class="alert alert-info text-center mt-3">
            <h4 class="alert-heading">Cargando</h4>
            <p><i class="fa fa-spinner fa-spin fa-2x"></i></p>
            <p class="mb-0">Espere por favor...</p>
        </div>               
    </div>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';
import HeroeModel from '../../models/Heroe.js';

export default {
    name: 'HeroesComponent',
    mounted(){
        this.cargando = false;
        this.getHeroes();
    },  
    data(){
        return {
            api: 'https://crud-frameworks-default-rtdb.europe-west1.firebasedatabase.app',
            cargando: false,
            heroes: HeroeModel
        }
    },     
    methods: {
        getHeroes(){
            this.cargando = true;
            axios.get(this.api + '/heroes.json')
                .then( res => {
                if(res.data){
                    this.heroes = this.heroesArray(res.data);
                    this.cargando = false;
                }
            });
        },
        heroesArray(heroesObj){
            const heroesData = [];

            if(heroesObj === null){
                return [];
            }

            Object.keys(heroesObj).forEach( key => {
                let heroe = HeroeModel;
                heroe = heroesObj[key];
                heroe.id = key;
                heroesData.push(heroe);
            });
            return heroesData;
        },        
        borrarHeroe(idHeroe){
            Swal.fire({
                title: "¿Estas seguro?",
                text: "Una vez eliminado, no podrá recuperar este archivo.",
                icon: "warning",
                buttons: true,
                dangerMode: true,
                })
                .then((willDelete) => {
                    if (willDelete) {
                        axios.delete(this.api + '/heroes/' + idHeroe + '.json')
                        .then(res => {
                            this.heroes = res.data;
                            this.getHeroes();
                        });
                    } else {
                        Swal.fire({
                            position: "top-end",
                            icon: "success",
                            title: "Tu archivo esta seguro.",
                            showConfirmButton: false,
                            timer: 1500
                        });                        
                    }
            });            

        }        
    }  
}
</script>