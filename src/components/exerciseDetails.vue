<script setup>
//Les composants
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'
import exerciseHelp from './exerciseDetailsComponents/exerciseHelp.vue'

// Transmission du contexte
const props = defineProps({
  ctx: Object,
  message: String
});

// Pour appeler une fonction externe
const emit = defineEmits(['manageActualUseRef'])
let n = 0   // Relatif à l'alternance d'exercice

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
  // Incrémentation du nombre de séries et du nombre n
  ctx.serie++
  n++
  // Si l'option d'alternance est activée et que ce n'est pas l'échauffement
  if (ctx.exercisesBook[ctx.type].alterne && !ctx.warmup) {
    ctx.indexExercise === 0 ? ctx.indexExercise++ : ctx.indexExercise-- // Alternance entre les deux exercices
    n % 2 === 0 ? ctx.serie = n / 2 : ctx.serie--;                      // Gestion du nombre de séries en fonction de n (une série et deux exercices de suite en mode alternance)
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (ctx.serie === ctx.actualUseRef.series) {
      ctx.serie = 0    // Reset le compteur de séries
      ctx.endSession = true  // Définis la fin de la séance
    }
  } else if (ctx.serie === ctx.actualUseRef.series) {
    // Réinitialisation du nombre de séries et du nombre n
    ctx.serie = 0
    n = 0
    // Si l'échauffement est actif
    if (ctx.warmup) {
      // Si tous les exercices ont été faits
      if (ctx.indexExercise === ctx.exercisesBook[ctx.type].echauffements.length - 1) {
        // On passe à l'exercice
        ctx.warmup = false
        ctx.indexExercise = 0
      } else {
        ctx.indexExercise++   // On incrémente
      }
    } else {
      // Si tous les exercices ont été faits
      if (ctx.indexExercise === ctx.exercisesBook[ctx.type].exercices.length - 1) {
        // On met fin à la session
        ctx.indexExercise = 0
        ctx.endSession = true
      } else {
        ctx.indexExercise++
      }
    }
  }
  emit('manageActualUseRef')
}

// Permet de sauter un exercice
function skipExercise() {
  const ctx = props.ctx
  // Définit le nombre de séries à l'avant-dernière ainsi que le nombre n
  ctx.serie = ctx.actualUseRef.series - 1
  n = ctx.actualUseRef.series * 2 - 1
  manageSession()
}
</script>

<template>
  <div class="blue-theme">
    <!--Bouton pour fermer la page-->
    <button aria-label="Close" class="btn-close mt-3 ms-3" type="button"
            @click="props.ctx.exercisePage = false; init()"></button>

    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ message }}</h1>

    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-boxes">
      <!--Le message de fin de série-->
      <div v-if="props.ctx.endSession" class="card-body">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <!--Les informations générales de l'exercice-->
      <exercise-information v-else :ctx="props.ctx"
                            class="card-body"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button aria-controls="instructions" aria-expanded="false" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5"
            data-bs-target="#instructions" data-bs-toggle="collapse" type="button" @click="init">
      Début /
      Arrêt
    </button>

    <!--Les instructions ou informations détaillées de l'exercice-->
    <exercise-instructions id="instructions" :ctx="props.ctx"
                           class="collapse" @manage-session="manageSession"/>

    <!--La page d'aide-->
    <exercise-help @skip-exercise="skipExercise"/>
  </div>
</template>