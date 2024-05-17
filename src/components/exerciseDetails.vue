<script setup>
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'
import { ref } from 'vue'
const props = defineProps({
  message: String,
  actualUseRef: Object,
  exercisesBook: Array,
  feats: Object
})

const emit = defineEmits(['manageActualUseRef', 'close', 'init'])
const callNext = ref(false)

// Détermine le nombre d'exercices dans un thème donné
function howManyExercises(dictionnary) {
  let array = Object.keys(dictionnary)  // Récupère toutes les clés de l'objet
  let final_array = array.filter((i) => !isNaN(parseInt(i)))  // Filtre pour ne ressortir qu'avec ceux qui se convertissent en nombre (donc les 1, 2, 3, ...)
  return final_array.length   // Renvoie la longueur du tableau soit le nombre d'exercices
}

// Gère le déroulement de la session
function manageSession() {
  props.feats.serie++   // Compte le nombre de séries
  // Si l'échauffement est actif
  if (props.feats.warmup) {
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (props.feats.serie === props.exercisesBook[props.feats.type].echauffement[props.feats.nbExercise].series) {
      props.feats.serie = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (props.feats.nbExercise === howManyExercises(props.exercisesBook[props.feats.type].echauffement)) {
        props.feats.warmup = false // Définis la fin de l'échauffement
        props.feats.nbExercise = 1 // Réinitialise l'exercice
      } else {
        props.feats.nbExercise++ // Compte le nombre d'exercices fait
      }
    }
  } else {
    // Si l'exercice à l'option d'alternance
    if (props.exercisesBook[props.feats.type].alterne) {
      props.feats.nbExercise === 1 ? props.feats.nbExercise++ : props.feats.nbExercise-- // Alternance entre les deux exercices
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (props.feats.serie === props.exercisesBook[props.feats.type][props.feats.nbExercise].series * 2) {
        props.feats.serie = 0    // Reset le compteur de séries
        props.feats.endSession = true  // Définis la fin de la séance
      }
    } else {
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (props.feats.serie === props.exercisesBook[props.feats.type][props.feats.nbExercise].series) {
        props.feats.serie = 0    // Reset le compteur de séries
        // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
        if (props.feats.nbExercise === howManyExercises(props.exercisesBook[props.feats.type])) {
          props.feats.endSession = true  // Définis la fin de la séance
        } else {
          props.feats.nbExercise++ // Compte le nombre d'exercices fait
        }
      }
    }
  }
  emit('manageActualUseRef')
}

function skipExercise(){
  callNext.value = true
}
</script>

<template>
  <div class="blue-theme">
    <!--Bouton pour fermer la page-->
    <button aria-label="Close" class="btn-close mt-3 ms-3" type="button" @click="$emit('close')"></button>

    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ message }}</h1>

    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-boxes">
      <!--Le message de fin de série-->
      <div v-if="props.feats.endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <exercise-information v-else :actual-use-ref="actualUseRef"
                            :alternating="!props.feats.warmup && props.exercisesBook[props.feats.type].alterne"
                            class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="$emit('init')">
      Début /
      Arrêt
    </button>

    <exercise-instructions ref="exeInstr" id="instructions" :actual-use-ref="actualUseRef" :exercises-book="props.exercisesBook"
                           :feats="{timeExercise: props.feats.timeExercise, restTime: props.feats.restTime,endSession:  props.feats.endSession, warmup: props.feats.warmup, callNext: callNext}"
                           class="collapse" @manage-session="manageSession"/>

    <exercise-help @skip-exercise="skipExercise()"/>
  </div>
</template>