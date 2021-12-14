<template>
    <div class="planContainer">
        <!-- Account -->
        <div class="accountInfo shadow">
            <div class="titleContainer">
            <img src="../assets/bank.png">
            <h3>Plan Ecoturism</h3>
        </div>
            <span><b>Usuario: </b> {{username}}</span>
            <span><b>Email: </b> {{email}}</span>
    </div>
     <!-- Creating plan -->
        <div class="planSection">
            <h4>Planes</h4>
            <input v-model="plan.usernameOrigin" class="form-control"  placeholder="idPlan">
            <input v-model="plan.usernameDestiny" class="form-control"  placeholder="idUsuario   ">
            <input v-model="plan.value" class="form-control"  placeholder="Valor">
            <input v-model="plan.value" class="form-control"  placeholder="fInicio">
            <input v-model="plan.value" class="form-control"  placeholder="fFin">
            <button v-on:click="createplan" class="btn btn-primary">Crear</button>
        </div>
        <!-- plan -->
        <table class="table table-striped planTable">
            <thead>
                <tr>
                    <th>idPlan</th>
                    <th>idUsuario</th>
                    <th>Valor</th>
                    <th>fInicio</th>
                    <th>fFin</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="plan in planByUsername" :key="plan.id">
                    <td>{{plan.idPlan}}</td>
                    <td>{{plan.idUsuario}}</td>
                    <td>{{plan.valor}}</td>
                    <td>{{plan.fInicio}}</td>
                    <td>{{plan.fFin}}</td>
                    <td >
                        <span  v-bind:class="{'negativeValue': plan.idPlan == idPlan}" class="planValue" v-if="plan.value">$ {{formatPrice(plan.value)}}</span>
                        <span v-if="!plan.value">0</span></td>
                </tr>
            </tbody>
        </table>
    </div>
         
</template>


<script>
import gql from "graphql-tag";

export default {
    name: "Plan", 
    data: function() {
        return {
            username: localStorage.getItem("username") || "none",
            accountByUsername: {
                email: "",
            },
            planByUsername: [],
            planByIdPlan: {
                idPlan: "",
                idUsuario: "",
                value: 0,
                fInicio: "",
                fFin: ""
           }
        }
    }, 
    apollo: {
        accountByUsername: {
            query: gql`
                query AccountByUsername($username: String!) {
                    accountByUsername(username: $username) {
                        email
                    }
                }
            `,
            variables() {
                return {
                    username: this.username,
                };
            },
        },
        planByUsername: {
            query: gql`
                query PlanByIdPlan($idPlan: String!) {
                    planByIdPlan(idPlan: $idPlan) {
                        idPlan
                        idUsuario
                        valor
                        fInicio
                        fFin
                        }
                        }
                    `,
                    variables(){
                        return {
                            username: this.username
                        }
                    }
        }
     
    },
    methods: {
    createPlan: async function(){
            this.plan.value = +this.plan.value; // Convierte a entero porque el apigateway lo espera asi
            await this.$apollo.mutate({
                mutation: gql`
                 mutation Mutation($plan: PlanInput!) {
                    createPlan(plan: $plan) {
                        idPlan
                        idUsuario
                        valor
                        fInicio
                        fFin
                    }
                }`,
                variables: {
                    plan: this.plan
                }
            })
            .then((result)=>{
                this.$apollo.queries.accountByUsername.refetch();
                 this.$apollo.queries.planByUsername.refetch();
                 this.plan.idPlan = ""
                 this.plan.idUsuario = ""
                 this.plan.value = 0
                 this.plan.fInicio = ""
                 this.plan.fFin = "" 
            })
            .catch((error)=>{
                console.log(error)
            })
        
        }
    }, 
    created: function () {
        this.$apollo.queries.accountByUsername.refetch();
        this.$apollo.queries.planByUsername.refetch();
    }
};
</script>



<style scoped>
    .planContainer{
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

    .planTable{
        margin-top: 25px;
    }

    .planValue{
        color: green;
    }

    .negativeValue{
        color: red;
    }

    .planSection{
        margin-top: 50px;
    }

    .planSection input{
        margin-right: 15px;
        width: 25%;
        display: inline;
    }
</style>