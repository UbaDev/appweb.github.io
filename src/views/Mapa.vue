<template>

    <div class="app container mt-6">
        <GmapMap :center="{ lat: 20.6122835, lng: -100.4802581 }" :zoom="12" map-type-id="terrain"
            style="width:100%; height:680px; margin-top:30px;">

            <div v-if="savedLocations.length > 0">

                <GmapMarker :key="index" v-for="(m, index) in savedLocations"
                    :position="{ lat: m.geoPoint.latitude, lng: m.geoPoint.longitude }" :icon="markerOptions" />

            </div>





        </GmapMap>
    </div>

</template>

<script>
import axios from "axios";

import db from "../firebase/firebaseInit";
const mapMarker = require('../assets/qw.png');
export default {
    name: "Home",
    components: {},
    data() {
        return {
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


        }

    },
    async beforeMount() {
        const snap = await db.collection("locations").get();

        snap.docs.forEach(doc => {
            this.savedLocations.push(doc.data())
        })
    },
    methods: {
        async handleFormSubmit() {
            const address = `${this.street}, ${this.city},${this.state}, ${this.zip} `
            let { data } = await axios.post("https://us-central1-rent-me-db-9a1d2.cloudfunctions.net/geocodeAddressAndSave", {
                address: address
            });

            const obj = {
                address: data.address,
                geoPoint: {
                    latitude: data.geoPoint._latitude,
                    longitude: data.geoPoint._longitude
                }
            }

            this.savedLocations.push(obj);

            this.street = "";
            this.city = "";
            this.state = "";
            this.zip = "";

        }
    }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400;500;600;700&display=swap");

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: "Quicksand", sans-serif;
}

.app {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
}

.container {
    max-width: 1440px;
    margin: 0 auto;
}

.link {
    cursor: pointer;
    text-decoration: none;
    text-transform: uppercase;
    color: black;
}

.link-light {
    color: #fff;
}
</style>
