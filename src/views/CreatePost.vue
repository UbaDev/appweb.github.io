<template>
    <div class="create-post">


        <div>
            <b-modal id="modal-prevent-closing" ref="modal" title="Pagar anuncio" @show="resetModal"
                @hidden="resetModal" @ok="handleOk">
                <form ref="form" @submit.stop.prevent="handleSubmit">
                    <b-img src="https://tusfinanzas.ec/wp-content/uploads/2017/07/credito-o-debito.png" fluid
                        alt="Responsive image"></b-img>

                    <b-form-group label="Numero de tarjeta" label-for="name-input"
                        invalid-feedback="Numero de tarjeta es requerido" :state="nameState">
                        <b-form-input placeholder="0000 - 0000 - 0000 - 0000" id="name-input" v-model="name"
                            :state="nameState" required></b-form-input>


                    </b-form-group>

                    <b-form-group label="Fecha de vencimiento" label-for="name-inpfut"
                        invalid-feedback="La fecha de vencimiento es requerida" :state="nameStaste">
                        <b-form-input placeholder="00 / 00" id="name-input" v-model="naame" :state="nameState" required>
                        </b-form-input>

                    </b-form-group>

                    <b-form-group label="Codigo de seguridad dinamico" label-for="name-iffnput"
                        invalid-feedback="El CVV es requerido" :state="nameStatsse">
                        <b-form-input placeholder="CVV" id="name-input" v-model="naaame" :state="nameState" required>
                        </b-form-input>


                    </b-form-group>
                    <b-img class="mr-3"
                        src="https://upload.wikimedia.org/wikipedia/commons/a/a4/Mastercard_2019_logo.svg" fluid
                        alt="Responsive image" width="80"></b-img>
                    <b-img class="mr-3"
                        src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/16/Former_Visa_%28company%29_logo.svg/1280px-Former_Visa_%28company%29_logo.svg.png"
                        fluid alt="Responsive image" width="80"></b-img>

                    <b-img class="mr-3"
                        src="https://graffica.info/wp-content/uploads/2018/04/american-express-graphic-design-3.jpg"
                        fluid alt="Responsive image" width="80"></b-img>
                </form>
            </b-modal>
        </div>















        <BlogCoverPreview v-show="this.$store.state.blogPhotoPreview" />
        <Loading v-show="loading" />
        <div class="container">
            <div :class="{ invisible: !error }" class="err-message">
                <p><span>Error: </span>{{ this.errorMsg }}</p>
            </div>
            <div class="blog-info">
                <input type="text" placeholder="Escribe el titulo del anuncio" v-model="blogTitle" />
                <div class="upload-file">
                    <label for="blog-photo">Subir foto de portada</label>
                    <input type="file" ref="blogPhoto" id="blog-photo" @change="fileChange"
                        accept=".png, .jpg, .jpeg" />
                    <button @click="openPreview" class="preview"
                        :class="{ 'button-inactive': !this.$store.state.blogPhotoFileURL }">
                        Vista previa
                    </button>
                    <span>Archivo elegido: {{ this.$store.state.blogPhotoName }}</span>
                </div>
            </div>
            <div class="editor">
                <vue-editor :editorOptions="editorSettings" v-model="blogHTML" useCustomImageHandler
                    @image-added="imageHandler" />
            </div>



            <div class="app formm container mt-6">
                <b-form @submit.prevent="handleFormSubmit">
                    <h2 class="mb-5">Formulario de dirección</h2>
                    <b-row>
                        <b-col md="6">
                            <b-form-group label="Calle">
                                <b-form-input v-model="street"></b-form-input>
                            </b-form-group>
                        </b-col>
                    </b-row>

                    <b-row>
                        <b-col md="3">
                            <b-form-group label="Ciudad">
                                <b-form-input v-model="city"></b-form-input>
                            </b-form-group>
                        </b-col>
                        <b-col md="3">
                            <b-form-group label="Estado">
                                <b-form-input v-model="state"></b-form-input>
                            </b-form-group>
                        </b-col>
                        <b-col md="3">
                            <b-form-group label="CP">
                                <b-form-input v-model="zip"></b-form-input>
                            </b-form-group>
                        </b-col>


                    </b-row>
                    <!--                     <b-button type="submit" variant="primary">Guardar</b-button>
 -->
                </b-form>

                <GmapMap :center="{ lat: 20.6122835, lng: -100.4802581 }" :zoom="12" map-type-id="terrain"
                    style="width:100%; height:500px; margin-top:60px;">

                    <div v-if="savedLocations.length > 0">

                        <GmapMarker :key="index" v-for="(m, index) in savedLocations"
                            :position="{ lat: m.geoPoint.latitude, lng: m.geoPoint.longitude }" :icon="markerOptions" />

                    </div>

                </GmapMap>
            </div>


            <div class="blog-actions">
                <button v-b-modal.modal-prevent-closing>Pagar y publicar anuncio</button>
                <router-link class="router-button" :to="{name: 'BlogPreview'}">Vista previa del anuncio</router-link>
            </div>
        </div>
    </div>
</template>

<script>
import BlogCoverPreview from "../components/BlogCoverPreview";
import firebase from "firebase/app";
import Loading from "../components/Loading";
import "firebase/storage";
import db from "../firebase/firebaseInit"
import Quill from "quill";
import axios from "axios";
const mapMarker = require('../assets/qw.png');
window.Quill = Quill;
const ImageResize = require('quill-image-resize-module').default;
Quill.register('modules/imageResize', ImageResize);
export default {
    name: "CreatePost",
    components : {
        BlogCoverPreview, Loading

    },
    data() {
        return {
            name: 'a',
            nameState: null,
            submittedNames: [],
            file:null,
            error: null,
            errorMsg: null,
            loading:null,
            editorSettings: {
                modules: {
                    imageResize:{},
                },
            },
            markerOptions: {
                url: mapMarker,
                size: { width: 90, height: 90, f: 'px', b: 'px', },
                scaledSize: { width: 50, height: 50, f: 'px', b: 'px', },
            },
            savedLocations: [],

            street: '',
            city: '',
            state: '',
            zip: '',

        };

    }, async beforeMount() {
        const snap = await db.collection("locations").get();

        snap.docs.forEach(doc => {
            this.savedLocations.push(doc.data())
        })
    },
    computed: {
        profileId(){
            return this.$store.state.profileId;
        },
        blogCoverPhotoName() {
            return this.$store.state.blogPhotoName;
        },
        blogTitle: {
            get() {
                return this.$store.state.blogTitle;
            },
            set(payload) {
                this.$store.commit('updateBlogTitle', payload);
            },
        },
        blogHTML: {
            get() {
                return this.$store.state.blogHTML;
            },
            set(payload) {
                this.$store.commit('newBlogPost', payload);
            },
        },
    },
    methods: {

        checkFormValidity() {
            const valid = this.$refs.form.checkValidity()
            this.nameState = valid
            return valid
        },
        resetModal() {
            this.name = ''
            this.nameState = null
        },
        handleOk(bvModalEvent) {
            // Prevent modal from closing
            bvModalEvent.preventDefault()
            // Trigger submit handler
            this.handleSubmit()
            if (this.blogTitle.length !== 0 && this.blogHTML.length !== 0) {
                if (this.file) {
                    this.loading = true;

                    const storageRef = firebase.storage().ref();
                    const docRef = storageRef.child(`documents/BlogCoverPhotos/${this.$store.state.blogPhotoName}`);
                    docRef.put(this.file).on(
                        "state_changed",
                        (snapshot) => {
                            console.log(snapshot);
                        },
                        (err) => {
                            console.log(err);
                            this.loading = false;
                        }, async () => {
                            const address = `${this.street}, ${this.city},${this.state}, ${this.zip} `
                            let { data } = await axios.post("https://us-central1-rent-me-db-9a1d2.cloudfunctions.net/geocodeAddressAndSave", {
                                address: address,
                            });




                            const downloadURL = await docRef.getDownloadURL();
                            const timestamp = await Date.now();
                            const dataBase = await db.collection("blogPosts").doc();
                            const dataBasee = await db.collection("locations").doc();




                            await dataBase.set({
                                blogID: dataBase.id,
                                blogHTML: this.blogHTML,
                                blogCoverPhoto: downloadURL,
                                blogCoverPhotoName: this.blogCoverPhotoName,
                                blogTitle: this.blogTitle,
                                profileId: this.profileId,
                                date: timestamp,

                            });

                            await dataBasee.set({
                                blogID: dataBase.id,
                                address: data.address,
                                geoPoint: {
                                    latitude: data.geoPoint._latitude,
                                    longitude: data.geoPoint._longitude
                                }


                            });


                            this.savedLocations.push(dataBasee);

                            this.street = "";
                            this.city = "";
                            this.state = "";
                            this.zip = "";
                            await this.$store.dispatch("getPost");

                            this.loading = false;
                            this.$router.push({ name: "ViewBlog", params: { blogid: dataBase.id } });
                        }
                    );
                    return;
                }
                this.error = true;
                this.errorMsg = "Por favor, asegurese que seleccionó uno foto de portada";
                setTimeout(() => {
                    this.error = false;
                }, 5000);
                return;
            }


            this.error = true;
            this.errorMsg = "Por favor, asegurese que todos los campos se encuentren llenos";
            setTimeout(() => {
                this.error = false;
            }, 5000);
            return;
        },
        handleSubmit() {
            // Exit when the form isn't valid
            if (!this.checkFormValidity()) {
                return
            }
            // Push the name to submitted names
            this.submittedNames.push(this.name)
            // Hide the modal manually
            this.$nextTick(() => {
                this.$bvModal.hide('modal-prevent-closing')
            })
        },

        fileChange(){
            this.file = this.$refs.blogPhoto.files[0];
            const fileName = this.file.name;
            this.$store.commit("fileNameChange", fileName);
            this.$store.commit("createFileURL" ,URL.createObjectURL(this.file));
        },
        openPreview(){
            this.$store.commit("openPhotoPreview");
        },
        imageHandler(file, Editor, cursorLocation, resetUploader) {
            const storageRef = firebase.storage().ref();
            const docRef = storageRef.child(`documents/blogPostPhotos/${file.name}`);
            docRef.put(file).on(
                "state_changed",
                (snapshot) => {
                    console.log(snapshot);
                },
                (err) => {
                    console.log(err);
                },
                async () => {
                    const downloadURL = await docRef.getDownloadURL();
                    Editor.insertEmbed(cursorLocation,"image",downloadURL);
                    resetUploader();
                }
            );
        },
           
        
            
           


        



    },
}
</script>

<style lang="scss">


.create-post {
    position:relative;
    height:100%;

    .formm {
        margin-top:30px;
    }

    button {
        margin-top:0px;
    }
    .router-button {
        text-decoration:none;
        color:#fff;
    }
    label,
    button,
    .router-button {
        
        transition:.5s ease-in-out all;
        align-self:center;
        font-size:14px;
        cursor:pointer;
        border-radius:20px;
        padding:12px 24px;
        color:#fff;
        background-color:#303030;
        text-decoration:none;

        &:hover {
          background-color:rgba(48,48,48, 0.7);
        }
    }

    .container {
        position:relative;
        height:100%;
        padding:10px 25px 60px;

    }

    .invisible {
        opacity:0 !important;

    }

    .err-message {
        width:100%;
        padding:12px;
        border-radius:8px;
        color:#fff;
        margin-bottom:10px;
        background-color:#303030;
        opacity:1;
        transition:.5s ease all;

        p {
            font-size:14px;

        }
        span {
            font-weight:600;
        }

    }

    .blog-info {
        display:flex;
        margin-bottom:32px;

        input:nth-child(1) {
            min-width:300px;
        }
        input{
            transition: .5s ease-in-out all;
            padding:10px 4px;
            border:none;
            border-bottom:1px solid #303030;
            &:focus {
                outline:none;
                box-shadow:0 1px 0 0 #303030;
            }
        }
        .upload-file {
            flex:1;
            margin-left:16px;
            position:relative;
            display:flex;

            input {
                display:none;
            }

            .preview {
                margin-left:16px;
                text-transform:initial;
            }
            span {
                font-size:12px;
                margin-left:16px;
                align-self:center;
            }
        }
    }

    .editor {
        height:60vh;
        display:flex;
        flex-direction:column;

        .quillWrapper {
            position:relative;
            display:flex;
            flex-direction:column;
            height:100%;
        }

        .ql-container {
            display:flex;
            flex-direction:column;
            height:100%;
            overflow:scroll;
        }

        .ql-editor {
            padding:20px 16px 30px;
        }
    }

    .blog-actions {
        
        button {
            margin-right:16px;
        }
    }

}
</style>