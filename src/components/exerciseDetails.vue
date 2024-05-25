<script setup>
//Les composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

const props = defineProps({
  ctx: Object,
  message: String
})

const emit = defineEmits(['manageActualUseRef'])

function init() {
  // Initialise le début d'une session
  props.ctx.endSession = false
  props.ctx.nbExercise = 1
  props.ctx.restTime = false
  props.ctx.serie = 0
  props.ctx.warmup = true
  emit('manageActualUseRef')
}


// Détermine le nombre d'exercices dans un thème donné
function howManyExercises(dictionary) {
  let array = Object.keys(dictionary)  // Récupère toutes les clés de l'objet
  let final_array = array.filter((i) => !isNaN(parseInt(i)))  // Filtre pour ne ressortir qu'avec ceux qui se convertissent en nombre (donc les 1, 2, 3, ...)
  return final_array.length   // Renvoie la longueur du tableau soit le nombre d'exercices
}

// Gère le déroulement de la session
function manageSession() {
  props.ctx.serie.value++  // Compte le nombre de séries
  // Si l'échauffement est actif
  if (props.ctx.warmup.value) {
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (props.ctx.serie.value === props.ctx.exercisesBook[props.ctx.type].echauffement[props.ctx.nbExercise.value].series) {
      props.ctx.serie.value = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (props.ctx.nbExercise.value === howManyExercises(props.ctx.exercisesBook[props.ctx.type].echauffement)) { // Idée : faire une variable plutôt qu'appeler à chaque fois la fonction → performance ?
        props.ctx.warmup.value = false // Définis la fin de l'échauffement
        props.ctx.nbExercise.value = 1 // Réinitialise l'exercice
      } else {
        props.ctx.nbExercise.value++ // Compte le nombre d'exercices fait
      }
    }
  } else {
    // Si l'exercice à l'option d'alternance
    if (props.ctx.exercisesBook[props.ctx.type].alterne) {
      props.ctx.nbExercise.value === 1 ? props.ctx.nbExercise.value++ : props.ctx.nbExercise.value-- // Alternance entre les deux exercices
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (props.ctx.serie.value === props.ctx.exercisesBook[props.ctx.type][props.ctx.nbExercise.value].series * 2) {
        props.ctx.serie.value = 0    // Reset le compteur de séries
        props.ctx.endSession.value = true  // Définis la fin de la séance
      }
    } else {
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (props.ctx.serie === props.ctx.exercisesBook[props.ctx.type][props.ctx.nbExercise.value].series) {
        props.ctx.serie = 0    // Reset le compteur de séries
        // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
        if (props.ctx.nbExercise.value === howManyExercises(props.ctx.exercisesBook[props.ctx.type])) {
          props.ctx.endSession.value = true  // Définis la fin de la séance
        } else {
          props.ctx.nbExercise.value++ // Compte le nombre d'exercices fait
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
    <button aria-label="Close" class="btn-close mt-3 ms-3" type="button" @click="ctx.exercisePage = false"></button>

    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ message }}</h1>

    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-boxes">
      <!--Le message de fin de série-->
      <div v-if="props.ctx.endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <exercise-information v-else :ctx="props.ctx"
                            class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="init">
      Début /
      Arrêt
    </button>

    <exercise-instructions id="instructions" :ctx="props.ctx"
                           class="collapse" @manage-session="manageSession"/>

    <exercise-help @skip-exercise="props.ctx.nbExercise++"/>
  </div>
</template>