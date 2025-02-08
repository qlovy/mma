<script setup>
import {computed, onUnmounted, ref} from 'vue'

// Les variables communes
const props = defineProps({
  currentExercise: Array,
  endSession: Boolean,
  noSerie: Number
})

// Permet l'appel d'une fonction extérieure
const emit = defineEmits(['updateSession'])

// Variables locales
const timeExercise = ref(false)
const audio = new Audio(require("/public/boxingBell.mp3"))
const restTime = ref(false)

// Définis les instructions à afficher
function instructionExercice(repetition) {
  // Si le paramètre "repetition" contient une chaîne de caractère
  if (typeof repetition === "string") {
    timeExercise.value = true // Définis l'affichage du temps d'exercice
    return "Tiens pendant " + repetition.slice(0, -1) + " " + repetition[repetition.length - 1] + "econdes"
  } else {
    timeExercise.value = false  // Fait disparaître l'affichage du temps d'exercice
    return "Fait " + repetition + " répétions"
  }
}

// Lance un timer pour un exercice
function launchExerciseTimer(repetition) {
  // Attrape seulement la partie avec le nombre, ex: "30s" => 30
  duration.value = parseInt(repetition.slice(0, repetition.length - 1)) * 1000
  reset()
}

// A chaque fin de série
function next() {
  restTime.value = true // Activation du temps de repos
  duration.value = props.currentExercise[3] * 1000 // Définition du temps de repos
  emit('updateSession') // Appel de la fonction externe
  // Si on est pas à la fin de la session
  if (!props.endSession) {
    // Début du temps de repos
    reset()
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
    restTime.value = false
    if (timeExercise.value === true) {
      sendNotif("Your time exercise is over")
      next()
      timeExercise.value = false
    } else {
      sendNotif("Your rest time is over")
    }
    audio.play()
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

function sendNotif(message) {
  const title = "MMA notification service"
  const options = {
    body: message,
    icon: ""
  }
  new Notification(title, options)
}
</script>

<template>
  <div>
    <div v-if="!props.endSession" class="card card-body blue-theme-boxes mx-5">
      <div v-if="!restTime">
        <!--L'instruction-->
        <p class="card-title ubuntu-regular fs-3">{{ instructionExercice(props.currentExercise[2]) }}</p>
        <div v-if="timeExercise">
          <label class="fs-4"
          >Temps d'exercice
            <progress :value="progressRate"></progress
            >
          </label>
          <div class="fs-5">{{ (countdown / 1000).toFixed(1) }}s</div>
          <button class="btn btn-primary mb-2 mt-2" type="button"
                  @click="launchExerciseTimer(props.currentExercise[2])">Lancer
          </button>
        </div>
        <!--Compteur de série-->
        <p class="card-text ubuntu-light-italic fs-5">Plus que {{
            props.currentExercise[1] - props.noSerie
          }}{{ props.currentExercise[1] - props.noSerie === 1 ? " série" : " séries" }}</p>
        <!--Le conseil-->
        <p class="card-text ubuntu-light-italic fs-5">{{ props.currentExercise[4] }}</p>
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