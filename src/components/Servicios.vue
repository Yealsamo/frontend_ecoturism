<template>
    <div class="serviciosContainer">
        <!-- Account -->
        <div class="accountInfo shadow">
            <div class="titleContainer">
                <img src="../assets/bank.png">
                <h3>Servicios Ecoturism</h3>
            </div>
            <span><b>Usuario: </b> {{username}}</span>
            <span><b>Servicio: </b> {{servicio}}</span>
            <span class="money">$ {{formatPrice(servicios.precio)}}</span> 
            <span v-if="accountByUsername.lastChange">{{accountByUsername.lastChange}}</span>
            <span v-if="!accountByUsername.lastChange"> No registra</span>
          </div> 
         <!-- Service -->
        <table class="table table-striped serviciosTable">
            <thead>
                <tr>
                    <th>servicio</th>
                    <th>precio</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="servicios in planByIdPlan" :key="servicios.id">
                    <td>{{Servicios.nombre}}</td>
                    <td >
                        <span  v-bind:class="{'negativeValue': servicios.nombre == nombre}" class="serviciosValue" v-if="servicios.value">$ {{formatPrice(servicio.value)}}</span>
                        <span v-if="!servicios.value">0</span></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>


<script>
import gql from "graphql-tag";

export default {
    name: "Servicios", 
    data: function() {
        return {
            username: localStorage.getItem("username") || "none",
            planByIdPlan: {
            servicios: {
                nombre: "",
                value: 0
            }
        }
        }
    },
    apollo:{
        planByIdPlan: {
            query: gql`
              query PlanByIdPlan($idPlan: String!) {
                planByIdPlan(idPlan: $idPlan) {
                    servicios {
                    nombre
                    precio
             }
         }
        }
      `,  
        variables() {
            return {
             username: this.username,
        }    
     } 
    } 
    }, 
    
    methods: {
        formatPrice(value) {
            let val = (value / 1).toFixed(2).replace(".", ",");
            return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
        },
    },
        create: function(){
            this.$apollo.queries.accountByUsername.refetch();
            this.$apollo.queries.planByIdPlan.refetch();;
            
        }
    
};
</script>


<style scoped>
    .serviciosContainer{
        width: 70%;
        margin: auto;
        margin-top: 50px;
    }
    .titleContainer{
    display: flex;
    align-items: center;
    margin-bottom: 5px;
    }
    .titleContainer h3{
        margin: 0;
        margin-left: 10px;
        font-family: Gotham;
        font-weight: 800;
    }
    .accountInfo{
            display: flex;
    flex-direction: column;
      padding: 25px 50px;

    }

    .money{
            font-size: 35px;
    }

    .serviciosTable{
        margin-top: 25px;
    }

    .serviciosValue{
        color: green;
    }

    .negativeValue{
        color: red;
    }

    .serviciosSection{
        margin-top: 50px;
    }

    .serviciosSection input{
        margin-right: 15px;
        width: 25%;
        display: inline;
    }
</style>