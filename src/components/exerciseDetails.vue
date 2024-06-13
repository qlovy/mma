<script setup>
//Les composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

const props = defineProps({
  ctx: Object,
  message: String
});

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

// Fonction qui gère le déroulement de la session
function manageSession() {
  const ctx = props.ctx
  ctx.serie++
  if (ctx.serie === ctx.actualUseRef.series) {
    ctx.serie = 0
    if (ctx.warmup) {
      if (ctx.indexExercise === ctx.exercisesBook[ctx.type].echauffements.length - 1) {
        ctx.warmup = false
        ctx.indexExercise = 0
      } else {
        ctx.indexExercise++
      }
    } else {
      if (ctx.indexExercise === ctx.exercisesBook[ctx.type].exercices.length - 1) {
        ctx.indexExercise = 0
        ctx.endSession = true
      } else {
        ctx.indexExercise++
      }
    }
  }
  emit('manageActualUseRef')
}

function skipExercise() {
  const ctx = props.ctx
  ctx.serie = ctx.actualUseRef.series - 1
  manageSession()
}

</script>

<template>
  <div class="blue-theme">
    <!--Bouton pour fermer la page-->
    <button aria-label="Close" class="btn-close mt-3 ms-3" type="button"
            @click="props.ctx.exercisePage = false"></button>

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

    <exercise-help @skip-exercise="skipExercise"/>
  </div>
</template>