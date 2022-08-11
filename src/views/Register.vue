<template>
    <div class="form-wrap">
        <Loading v-if="loading" />
        <form class="register">
            <p class="login-register">
                ¿Ya tienes una cuenta?
                <router-link class="router-link" :to="{ name: 'Login' }">Iniciar sesión</router-link>
            </p>

            <h2>Forma parte de Rent-me</h2>
            <div class="inputs">
                <div class="input">
                    <input type="text" placeholder="Nombre" v-model="firstName">
                    <user class="icon" />
                </div>
                <div class="input">
                    <input type="text" placeholder="Apellidos" v-model="lastName">
                    <user class="icon" />
                </div>
                <div class="input">
                    <input type="text" placeholder="Nombre de usuario" v-model="username">
                    <user class="icon" />
                </div>
                <div class="input">
                    <input type="text" placeholder="Correo electronico" v-model="email">
                    <email class="icon" />
                </div>

                <div class="input">
                    <input type="password" placeholder="Contraseña" v-model="password">
                    <password class="icon" />
                </div>
                <div v-show="error" class="error">{{this.errorMsg}}</div>
            </div>
            <button @click.prevent="register" type="button">Registrarme</button>
            <div class="angle"></div>

        </form>
        <div class="background">
        </div>


    </div>
</template>

<script>
import email from "../assets/Icons/envelope-regular.svg";
import password from "../assets/Icons/lock-alt-solid.svg";
import user from "../assets/Icons/user-alt-light.svg";
import firebase from "firebase/app"
import "firebase/auth";
import Loading from "../components/Loading";

import db from "../firebase/firebaseInit";
export default {
    name: "Register",
    components: { password, email, user, Loading },
    data() {
        return {
            firstName: "",
            lastName: "",
            username: "",
            email: "",
            password: "",
            error:null,
            errorMsg:"",
            loading:null,
        };

    },

    methods: {
        async register() {
            if (this.email !== "" &&
                this.password !== "" &&
                this.firstName !== "" &&
                this.lastName !== "" &&
                this.username !== ""
            ) {
                this.error = false;
                this.errorMsg = "";
               
                const firebaseAuth = await firebase.auth();
                this.loading = true;
                const createUser = await firebaseAuth.createUserWithEmailAndPassword(this.email, this.password);
                const result = await createUser;
                this.loading = false;
                const dataBase = db.collection("users").doc(result.user.uid);
                await dataBase.set({
                    firstName : this.firstName,
                    lastName: this.lastName,
                    username : this.username,
                    email: this.email,
                });
                
                this.$router.push({name:"Home"});
                return;
            }
            this.error = true;
            this.loading = false;
            this.errorMsg = "Los campos no puede quedar vacios";
        },
    },
};
</script>

<style lang="scss" scoped>
.register {
    max-width: 550px;
}
</style>