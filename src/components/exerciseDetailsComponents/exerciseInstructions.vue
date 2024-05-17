<script setup>
import {computed, onUnmounted, ref, watch } from 'vue'

const props = defineProps({
  actualUseRef: Object,
  feats: Object,
  exercisesBook: Array
})

const emit = defineEmits(['manageSession'])

watch(() => props.feats.callNext, () => {
  next()
  props.feats.callNext = false
})

// Définis les instructions à afficher
function instructionExercice(repetition) {
  // Si le paramètre repetition contient une chaîne de caractère
  if (typeof repetition === "string") {
    // Le chiffre contenu dans la chaine de caractère est plus petit que 10
    if (parseInt(repetition.slice(0, 2)) > 10) {
      props.feats.timeExercise = true // Définis l'affichage du temps d'exercice
    }
    return "Tiens pendant " + repetition + "econdes"
  } else {
    props.feats.timeExercise = false  // Fait disparaître l'affichage du temps d'exercice
    return "Fait " + repetition + " répétions"
  }
}

// Lance un timer pour un exercice
function launchExerciseTimer(repetition) {
  duration.value = parseInt(repetition.slice(0, 2)) * 1000
  reset()
}

// Gère le passage d'une série à une autre ou d'un exercice à un autre
function next() {
  props.feats.restTime = true // Définis l'affichage du timer
  // Définis le temps de repos
  // Si l'échauffement est actif
  if (props.feats.warmup) {
    duration.value = props.exercisesBook[props.feats.type].echauffement[props.feats.nbExercise].recuperation * 1000
  } else if (props.exercisesBook[props.feats.type].alterne && props.feats.serie % 2 === 0) {
    // Si l'option d'alternance est activée et qu'on a complété une série
    duration.value = props.exercisesBook[props.feats.type][props.feats.nbExercise].recuperation * 500  // le temps est réduit de moitié
  } else {
    duration.value = props.exercisesBook[props.feats.type][props.feats.nbExercise].recuperation * 1000
  }
  // Appelle les fonctions du composant "parent"
  emit('manageSession')
  //  Si la session n'est pas finie
  if (!props.feats.endSession) {
    reset() // Lance le décompte
  }
}

/*
 TIMER
*/

const duration = ref(1000) // Définit la valeur à 1 seconde
const countdown = ref(0)

let lastTime
let handle

// Le décompte du temps
const update = () => {
  countdown.value = duration.value - (performance.now() - lastTime)
  // Quand le temps est écoulé.
  if (countdown.value <= 0) {
    countdown.value = 0
    cancelAnimationFrame(handle)
    props.feats.restTime = false
    if (props.feats.timeExercise === true) {
      next()
      props.feats.timeExercise = false
    }
  } else {
    handle = requestAnimationFrame(update)
  }
}

// La réinitialisation du timer
const reset = () => {
  countdown.value = duration.value
  lastTime = performance.now()
  update()
}

// L'évolution de la barre
const progressRate = computed(() =>
    Math.min(countdown.value / duration.value, 1)
)

onUnmounted(() => {
  cancelAnimationFrame(handle)
})
</script>

<template>
  <div>
    <div v-if="!props.feats.endSession" class="card card-body blue-theme-boxes mt-2 mx-5">
      <div v-if="!props.feats.restTime">
        <!--L'instruction-->
        <p class="card-title ubuntu-regular fs-3">{{ instructionExercice(actualUseRef.repetitions) }}</p>
        <div v-if="props.feats.timeExercise">
          <label class="fs-4"
          >Temps d'exercice
            <progress :value="progressRate"></progress
            >
          </label>
          <div class="fs-5">{{ (countdown / 1000).toFixed(1) }}s</div>
          <button class="btn btn-primary mb-2 mt-2" type="button"
                  @click="launchExerciseTimer(actualUseRef.repetitions)">Lancer
          </button>
        </div>
        <!--Compteur de série-->
        <p class="card-text ubuntu-light-italic fs-5">Plus que {{
            actualUseRef.series - serie
          }}{{ actualUseRef.series - serie === 1 ? " série" : " séries" }}</p>
        <!--Le conseil-->
        <p class="card-text ubuntu-light-italic fs-5">{{ actualUseRef.conseil }}</p>
        <!--Changement étape-->
        <button v-if="!timeExercise" class="btn btn-primary mt-2 fs-5 w-100" type="button" @click="next()">Suivant
        </button>
      </div>
      <div v-else>
        <!--Le temps de pause-->
        <label class="fs-4">Temps de repos
          <progress :value="progressRate"></progress>
        </label>
        <div class="fs-5">{{ (countdown / 1000).toFixed(1) }}s</div>
      </div>
    </div>
  </div>
</template>

<style>

</style>