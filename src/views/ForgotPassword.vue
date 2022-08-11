<template>
    <div class="reset-password">
        <Modal v-if="modalActive" :modalMessage="modalMessage" v-on:close-modal="closeModal" />
        <Loading v-if="loading" />
        <div class="form-wrap">
            <form class="reset">
                <p class="login-register">
                    Regresar a
                    <router-link class="router-link" :to="{ name: 'Login' }"> Mi cuenta</router-link>
                </p>
                <h2>Restablecer contraseña</h2>
                <p>¿Olvidaste tu contreseña?</p>
                <div class="inputs">
                    <div class="input">
                        <input type="text" placeholder="Correo electronico" v-model="email">
                        <email class="icon" />
                    </div>
                </div>
                <small class="under-text">Escribe tu correo electronico para recuperarla</small>
                <button @click.prevent="resetPassword">Restablecer</button>
                <div class="angle"></div>
            </form>
            <div class="background"> </div>
        </div>
    </div>


</template>

<script>
import Modal from "../components/Modal"
import Loading from "../components/Loading"
import email from "../assets/Icons/envelope-regular.svg";

import firebase from "firebase/app"
import "firebase/auth";




export default {
    name: "ForgotPassword",
    components: { email, Modal, Loading },
    data() {
        return {
            email: null,
            modalActive:false,
            modalMessage:"",
            loading:null,
        };
        

    },
    methods: {
        
        resetPassword(){
            this.loading = true;
            firebase.auth().sendPasswordResetEmail(this.email).then(() => {
                this.modalMessage = "Si tu cuenta existe, recibirás un correo electronico para restablecer tu contraseña";
                this.loading = false;
                this.modalActive= true;
           }).catch( ()=> {
                this.modalMessage = "El correo electronico no tiene un formato valido"; 
                this.loading = false;
                this.modalActive= true;
           });
        },
        closeModal() {
            this.modalActive = !this.modalActive;
            this.email = "";
        }
        
    }
}
</script>

<style lang="scss" scoped>
.reset-password {
    position: relative;

    .form-wrap {
        .reset {

            h2 {
                margin-bottom: 8px;
            }

            p {
                text-align: center;
                margin-bottom: 32px;
            }

            span {
                margin: 10px;
            }
        }
    }
}
</style>