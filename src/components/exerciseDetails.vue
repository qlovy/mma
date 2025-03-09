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
const noMaxExercise = props.currentTraining.exercices.length - 1
let noMaxSerie = 0
let arr =  [0, 0]

if (props.currentTraining.type === "circuit"){
  // Trouver le no max de série
  for (let i=0; i<props.currentTraining.exercices.length - 1;i++){
    if (props.currentTraining.exercices[i][1] > noMaxSerie){
      noMaxSerie = props.currentTraining.exercices[i][1]
    }
  }

  // Trouver la plage d'index pour le circuit
  for (let i=0; i<props.currentTraining.exercices.length-1; i++){
    if (props.currentTraining.exercices[i][1] === noMaxSerie){
      if (arr[0] === 0){
        arr[0] = i
      }else{
        arr[1] = i
      }
    }
  }
}

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
  // Si la séance est de type "circuit" et que l'index correspond à la plage d'exercice concerné
  if (props.currentTraining.type === "circuit" && noExercise >= arr[0] && noExercise <= arr[1]){
    noExercise++
    // Si on est au dernier exercice
    if (noExercise === arr[1] + 1){
      // Si le nombre de série effectuée correspond au nombre de série max à faire.
      if (noSerie.value === noMaxSerie-1){
        noSerie.value = 0  // Réinitalisation des séries
      }else{
        noSerie.value++      // Comptage d'une série
        noExercise = arr[0]  // Retour au premier exercice
      }
    }
  }else{
    noSerie.value++ // Compatge du nombre de série effectuée
    // Si le nombre de série effectuée est égal au nombre de série à effectuer
    if (noSerie.value === currentExercise.value[1]) {
      noSerie.value = 0  // Réinitialisation du compteur
      // Si le nombre d'exercice effectués est égal au nombre d'exercices à effectuer
      if (noExercise === noMaxExercise) {
        endSession.value = true // La séance est terminée
      } else {
        noExercise++ // Comptage du nombre d'exercice effectué
      }
    }
  }
  currentExercise.value = props.currentTraining.exercices[noExercise]  // Actualisation de l'exercice actuel
}

// Gestion du saut d'exercice
function skipExercise() {
  if (props.currentTraining.type !== "circuit" && noExercise >= arr[0] && noExercise <= arr[1]){
    noSerie.value = props.currentTraining.exercices[noExercise][1] - 1  // Simule l'exécution complète de toutes les séries
  }
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