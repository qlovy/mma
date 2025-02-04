<script setup>
import {computed, onUnmounted, ref} from 'vue'

const props = defineProps({
  currentExercise: Array,
  endSession: Boolean,
  noSerie: Number
})

const restTime = ref(false);

// Appel une fonction extérieure
const emit = defineEmits(['updateSession'])

// Variables spécifiques au bloc
const timeExercise = ref(false)
const audio = new Audio(require("/public/boxingBell.mp3"))

// Définis les instructions à afficher
function instructionExercice(repetition) {
  // Si le paramètre repetition contient une chaîne de caractère
  if (typeof repetition === "string") {
    // Le chiffre contenu dans la chaine de caractère est plus grand que 10
    // Pourquoi ? plus que 10
    if (parseInt(repetition.slice(0, repetition.length - 1)) > 10) {
      timeExercise.value = true // Définis l'affichage du temps d'exercice
    }
    return "Tiens pendant " + repetition.slice(0, -1) + " " + repetition[repetition.length-1] + "econdes"
  } else {
    timeExercise.value = false  // Fait disparaître l'affichage du temps d'exercice
    return "Fait " + repetition + " répétions"
  }
}

// Lance un timer pour un exercice
function launchExerciseTimer(repetition) {
  // Attrape seulement la partie avec le nombre
  duration.value = parseInt(repetition.slice(0, repetition.length - 1)) * 1000
  reset()
}

function next(){
  restTime.value = true
  duration.value = props.currentExercise[2] * 1000;
  emit('updateSession')
  if (!props.endSession){
    reset()
  }
}

/*
// Gère le passage d'une série à une autre ou d'un exercice à un autre
function next() {
  const ctx = props.ctx
  ctx.restTime = true // Définis l'affichage du timer

  // Définis le temps de repos
  // Si l'échauffement est actif
  if (ctx.warmup) {
    duration.value = ctx.exercisesBook[ctx.type].echauffements[ctx.indexExercise].recuperation * 1000
    // Si on a une option d'alternance et que le nombre de séries est paire
  } else if (ctx.exercisesBook[ctx.type].alterne && ctx.serie % 2 === 0) {
    // Si l'option d'alternance est activée et qu'on a complété une série
    duration.value = ctx.exercisesBook[ctx.type].exercices[ctx.indexExercise].recuperation * 500  // le temps est réduit de moitié
  } else {
    duration.value = ctx.exercisesBook[ctx.type].exercices[ctx.indexExercise].recuperation * 1000
  }

  // Appelle les fonctions du composant "parent"
  emit('updateSession')
  //  Si la session n'est pas finie
  if (!ctx.endSession) {
    reset() // Lance le décompte
  }
}
*/

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
    }else{
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

function sendNotif(message){
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