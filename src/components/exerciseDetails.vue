<script setup>
import {ref} from "vue";

// Importation des composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

// Variables communes
const props = defineProps({
  exercisePage: Boolean,
  currentTraining: Object
});

// Variables locales
let noExercise = 0
const noSerie = ref(0)
const endSession = ref(false)
const currentExercise = ref(props.currentTraining.exercices[noExercise])

// Evénements externes
const emit = defineEmits(['close'])

// Initalisation d'une nouvelle séance
function init() {
  noExercise = 0
  noSerie.value = 0
  endSession.value = false
}

// Gestion du déroulement de la séance
function updateSession() {
  noSerie.value++ // Compatge du nombre de série effectuée
  // Si le nombre de série effectuée est égal au nombre de série à effectuer
  if (noSerie.value === props.currentTraining.exercices[noExercise][1]) {
    noSerie.value = 0  // Réinitialisation du compteur
    // Si le nombre d'exercice effectués est égal au nombre d'exercices à effectuer
    if (noExercise === props.currentTraining.exercices.length - 1) {
      endSession.value = true // La séance est terminée
    } else {
      noExercise++ // Comptage du nombre d'exercice effectué
    }
    currentExercise.value = props.currentTraining.exercices[noExercise]  // Actualisation de l'exercice actuel
  }
}

// Gestion du saut d'exercice
function skipExercise() {
  noSerie.value = props.currentTraining.exercices[noExercise][1] - 1  // Simule l'exécution complète de toutes les séries
  updateSession()
}
</script>

<template>
  <div style="background-color: #15baba;">
    <!--Bouton pour fermer la page de l'exercice-->
    <button aria-label="Close" class="btn-close mt-3 ms-3" type="button"
            @click="emit('close')"></button>

    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ props.currentTraining.nom }}</h1>

    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-boxes">
      <!--Le message de fin de série-->
      <div v-if="endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <!--Les informations générales de l'exercice-->
      <exerciseInformation v-else :data="currentExercise"
                           class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-4"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="init">
      Début /
      Arrêt
    </button>

    <!--Les instructions ou informations détaillées de l'exercice, section collapse-->
    <exerciseInstructions id="instructions" :currentExercise="currentExercise" :endSession="endSession"
                          :noSerie="noSerie"
                          class="collapse" @update-session="updateSession"/>

    <!--La page d'aide-->
    <exerciseHelp @skip-exercise="skipExercise"/>
  </div>
</template>