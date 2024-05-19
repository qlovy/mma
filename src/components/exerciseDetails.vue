<script setup>
import { ref } from 'vue'

//Les composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

const props = defineProps({
  actualUseRef: Object,
  exercisesBook: Array,
  type: Number,
  message: String
})

const serie = defineModel('serie')
const nbExercise = defineModel('nbExercise')
const restTime = defineModel('restTime')
const warmup = defineModel('warmup')
const endSession = defineModel('endSession')
const callNext = ref(false)

const emit = defineEmits(['manageActualUseRef', 'close', 'init'])

// Détermine le nombre d'exercices dans un thème donné
function howManyExercises(dictionnary) {
  let array = Object.keys(dictionnary)  // Récupère toutes les clés de l'objet
  let final_array = array.filter((i) => !isNaN(parseInt(i)))  // Filtre pour ne ressortir qu'avec ceux qui se convertissent en nombre (donc les 1, 2, 3, ...)
  return final_array.length   // Renvoie la longueur du tableau soit le nombre d'exercices
}

// Gère le déroulement de la session
function manageSession() {
  serie.value++   // Compte le nombre de séries
  // Si l'échauffement est actif
  if (warmup) {
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie === props.exercisesBook[props.type].echauffement[nbExercise.value].series) {
      serie.value = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (nbExercise.value === howManyExercises(props.exercisesBook[props.type].echauffement)) {
        warmup.value = false // Définis la fin de l'échauffement
        nbExercise.value = 1 // Réinitialise l'exercice
      } else {
        nbExercise.value++ // Compte le nombre d'exercices fait
      }
    }
  } else {
    // Si l'exercice à l'option d'alternance
    if (props.exercisesBook[props.type].alterne) {
      nbExercise.value === 1 ? nbExercise.value++ : nbExercise.value-- // Alternance entre les deux exercices
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (serie.value === props.exercisesBook[props.type][nbExercise.value].series * 2) {
        serie.value = 0    // Reset le compteur de séries
        endSession.value = true  // Définis la fin de la séance
      }
    } else {
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (serie.value === props.exercisesBook[props.type][nbExercise.value].series) {
        serie.value = 0    // Reset le compteur de séries
        // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
        if (nbExercise.value === howManyExercises(props.exercisesBook[props.type])) {
          endSession.value = true  // Définis la fin de la séance
        } else {
          nbExercise.value++ // Compte le nombre d'exercices fait
        }
      }
    }
  }
  emit('manageActualUseRef')
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
      <div v-if="endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <exercise-information v-else :actual-use-ref="actualUseRef"
                            :alternating="!warmup && props.exercisesBook[props.type].alterne"
                            class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="$emit('init')">
      Début /
      Arrêt
    </button>

    <exercise-instructions id="instructions" :actual-use-ref="actualUseRef" :exercises-book="props.exercisesBook"
                           :type="props.type"
                           v-model:serie="serie" v-model:nb-exercise="nbExercise" v-model:rest-time="restTime" v-model:warmup="warmup" v-model:endSession="endSession" v-model:callNext="callNext"
                           class="collapse" @manage-session="manageSession"/>

    <exercise-help @skip-exercise="callNext=true"/>
  </div>
</template>