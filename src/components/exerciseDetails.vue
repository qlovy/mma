<script setup>
//Les composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

const props = defineProps(['ctx', 'message']);

const emit = defineEmits(['manageActualUseRef'])

function init() {
  const ctx = props.ctx
  // Initialise le début d'une session
  ctx.endSession = false
  ctx.indexExercise = 0
  ctx.restTime = false
  ctx.serie = 0
  ctx.warmup = true
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
  const ctx = props.ctx
  ctx.serie++  // Compte le nombre de séries
  // Si l'échauffement est actif
  if (ctx.warmup) {
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (ctx.serie === ctx.exercisesBook[ctx.type].echauffements[ctx.indexExercise].series) {
      ctx.serie = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (ctx.indexExercise === howManyExercises(ctx.exercisesBook[ctx.type].echauffements)) { // Idée : faire une variable plutôt qu'appeler à chaque fois la fonction → performance ?
        ctx.warmup = false // Définis la fin de l'échauffement
        ctx.indexExercise = 1 // Réinitialise l'exercice
      } else {
        ctx.indexExercise++ // Compte le nombre d'exercices fait
      }
    }
  } else {
    // Si l'exercice à l'option d'alternance
    if (ctx.exercisesBook[ctx.type].alterne) {
      ctx.indexExercise === 1 ? ctx.indexExercise++ : ctx.indexExercise-- // Alternance entre les deux exercices
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (ctx.serie === ctx.exercisesBook[ctx.type][ctx.indexExercise].series * 2) {
        ctx.serie = 0    // Reset le compteur de séries
        ctx.endSession = true  // Définis la fin de la séance
      }
    } else {
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (ctx.serie === ctx.exercisesBook[ctx.type][ctx.indexExercise].series) {
        ctx.serie = 0    // Reset le compteur de séries
        // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
        if (ctx.indexExercise === howManyExercises(ctx.exercisesBook[ctx.type])) {
          ctx.endSession = true  // Définis la fin de la séance
        } else {
          ctx.indexExercise++ // Compte le nombre d'exercices fait
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
      <div v-if="ctx.endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <exercise-information v-else :ctx="ctx"
                            class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="init">
      Début /
      Arrêt
    </button>

    <exercise-instructions id="instructions" :ctx="ctx"
                           class="collapse" @manage-session="manageSession"/>

    <exercise-help @skip-exercise="ctx.indexExercise++"/>
  </div>
</template>