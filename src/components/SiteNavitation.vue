<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{name: 'home'}">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">Météo Américaine</p>
                </div>
            </RouterLink>

            <div class="flex gap-3 flex-1 justify-end">
                <i 
                    class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" 
                    @click="toggleModal"
                ></i>
                <i 
                    class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="addCity"
                    v-if="route.query.preview"
                ></i>
            </div>

            <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
                <div class="text-black">
                    <h1 class="text-2xl mb-1">About:</h1>
                    <p class="mb-4">
                        Cette application permet de traquer la météo des villes américaines.
                    </p>
                    <p class="mb-4">
                        Cette application à été crée en suivant un tutorial de la chaine de NetNinja et constitue un entrainement à l'utilisation du Framework VueJs.
                        Pour le moment j'ai suivi le tutorial utilisant des API et des villes américaines.
                        J'ai comme piste d'amélioration de trouver des API météo française et d'adapter ce site pour la France.
                    </p>
                    <h2 class="text-2xl">Utilisation:</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>Cherchez une ville en la cherchant la barre de recherche.</li>
                        <li>Selectionnez une ville dans les résultats, vous serez redirigé vers les informations relativées à la météo locale.</li>
                        <li>Vous pouvez ensuite ajouter cette ville à la page d'accueil en utilisant le boutton "+" en haut à droite.</li>
                    </ol>

                    <h2 class="text-2xl">Retirer une ville de la liste</h2>
                    <p>Si vous ne désirez plus suivre la météo d'une ville, selectionnez une ville. Ensuite, utilisez l'option "Retirer la ville" en bas de la page.</p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from "vue";
import { uid } from "uid";
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from './BaseModal.vue';

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
    if(localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    }

    const locationObj = {
        id : uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng,
        }
    };

    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query})
}

const modalActive = ref(null);
const toggleModal= () => {
    modalActive.value = !modalActive.value
}
</script>