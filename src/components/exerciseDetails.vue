<script setup>
import exerciseInformation from './exerciseDetailsComponents/exerciseInformations.vue'
import exerciseInstructions from './exerciseDetailsComponents/exerciseInstructions.vue'

const props = defineProps({
  message: String,
  actualUseRef: Object,
  exercisesBook: Array,
  type: Number,
  warmup: Boolean,
})

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
  if (warmup.value) {
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie.value === exercisesBook[type.value].echauffement[nbExercise.value].series) {
      serie.value = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (nbExercise.value === howManyExercises(exercisesBook[type.value].echauffement)) {
        warmup.value = false // Définis la fin de l'échauffement
        nbExercise.value = 1 // Réinitialise l'exercice
      } else {
        nbExercise.value++ // Compte le nombre d'exercices fait
      }
    }
  } else {
    // Si l'exercice à l'option d'alternance
    if (exercisesBook[type.value].alterne) {
      nbExercise.value === 1 ? nbExercise.value++ : nbExercise.value-- // Alternance entre les deux exercices
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (serie.value === exercisesBook[type.value][nbExercise.value].series * 2) {
        serie.value = 0    // Reset le compteur de séries
        endSession.value = true  // Définis la fin de la séance
      }
    } else {
      // Si le nombre de séries faites correspondent à celles qui doivent être faites
      if (serie.value === exercisesBook[type.value][nbExercise.value].series) {
        serie.value = 0    // Reset le compteur de séries
        // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
        if (nbExercise.value === howManyExercises(exercisesBook[type.value])) {
          endSession.value = true  // Définis la fin de la séance
        } else {
          nbExercise.value++ // Compte le nombre d'exercices fait
        }
      }
    }
  }
}

</script>

<template>
  <div class="blue-theme">
    <!--Bouton pour fermer la page-->
    <button type="button" class="btn-close mt-3 ms-3" aria-label="Close" @click="$emit('close')"></button>

    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ message }}</h1>

    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-boxes">
      <!--Le message de fin de série-->
      <div class="card-body" v-if="endSession">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <exercise-information class="card-body" v-else :actual-use-ref="actualUseRef" :alternating="!warmup && exercisesBook[type].alterne"/>
    </div>

    <!--Bouton qui débute la session, affiche la section collapse-->
    <button type="button" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5" data-bs-toggle="collapse"
            data-bs-target="#instructions" aria-expanded="false" aria-controls="instructions" @click="$emit('init')">Début /
      Arrêt
    </button>

    <exercise-instructions class="collapse" id="instructions" :actual-use-ref="actualUseRef" :feats="Object(timeExercise, restTime, endSession, warmup)" :exercises-book="exercisesBook" />

    <!--Bouton pour la page d'aide-->
    <button type="button" class="btn fixed-bottom ms-2 mb-2" @click="help = true">Besoin d'aide ?</button>
    <!--Page d'aide-->
    <Transition>
      <div id="help-page" v-if="help" class="fixed-bottom mb-3">
        <div class="card card-body mx-3">
          <!--Bouton pour fermer la page-->
          <button type="button" class="btn-close" aria-label="Close" @click="help = false"></button>
          <p class="card-text ubuntu-regular fs-3 mx-auto">Que voulez-vous faire ?</p>
          <!--Action: passer l'exercice-->
          <button type="button" class="btn ubuntu-light fs-5" @click="skipExercise()">Passer un exercice</button>
        </div>
      </div>
    </Transition>
  </div>
</template>