<script setup>
import { ref, onUnmounted, computed } from 'vue'
const message = ref('')
const dictonnaryExercices = ref({
  poussee: {
    1: createExercice('Pompes Pikes', 6, 6, 60, 'Tu dois sentir tes épaules tout le long. Pense à avoir les avant-bras à 90 degrés du sol.'),
    2: createExercice('Pseudo pompes', 6, 6, 60, 'Main à 45 degrés. Sentir une pression sur les biceps et engager les abdos ainsi que les déltoïdes.'),
    alternate: true
  },
  tirage: {
    1: createExercice('Traction', 5, 6, 60, ''),
    2: createExercice('Front lever row', 5, "5 s", 60, ''),
    3: createExercice('Hanging', 1, "60 s", 60, ''),
    4: createExercice('Dips', 4, 6, 60, ''),
    alternate: false
  },
  jambes: {
    1: createExercice('Flexions plantaires', 4, 20, 30, ''),
    2: createExercice('Split squat', 3, 10, 60, ''),
    3: createExercice('Curl nordique', 4, 5, 60, ''),
    4: createExercice('Squat complet', 4, 10, 30, ''),
    5: createExercice('Chaise', 1, "60 s", 30, ''),
    alternate: false
  }
})
// TODO: Ajout automatique des divs en fonction du tableau ci-dessus
const serie = ref(0)
const type = ref("")
const restTime = ref(false)
const endSession = ref(false)
const nbExercise = ref(1)

function createExercice(name, series, reps, rest_in_s, advice){
  return {nom: name, series: series, repetitions: reps, recuperation: rest_in_s, conseil: advice}
}
function messageNew (origin){
  if (origin === 1) {
    message.value = 'Poussée'
    type.value = "poussee"
  }else if (origin === 2){
    message.value = 'Tirage'
    type.value = "tirage"
  }else if (origin === 3){
    message.value = 'Jambes'
    type.value = "jambes"
  }else{
    message.value = ''
    init()  // Réinitialise tous les exercices quand on quitte une session
  }
}

function next() {
  restTime.value = true // Affichage du timer
  // Si l'option d'alternance est activée et qu'on a complété une série
  if (dictonnaryExercices.value[type.value].alternate && serie.value % 2 === 0){
    duration.value = dictonnaryExercices.value[type.value][nbExercise.value].recuperation * 500
  }else{
    duration.value = dictonnaryExercices.value[type.value][nbExercise.value].recuperation * 1000
  }
  reset() // Lance le décompte
}


function manageSession() {
  serie.value++
  if (dictonnaryExercices.value[type.value].alternate){
    nbExercise.value === 1 ? nbExercise.value++ : nbExercise.value-- // Alternance entre les deux exercices
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie.value === dictonnaryExercices.value[type.value][nbExercise.value].series * 2){
      serie.value = 0    // Reset le compteur de séries
      endSession.value = true  // Définis la fin de la séance
    }
  }else{
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie.value === dictonnaryExercices.value[type.value][nbExercise.value].series){
      nbExercise.value++ // Compte le nombre d'exercices fait
      serie.value = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (nbExercise.value > Object.keys(dictonnaryExercices.value[type.value]).length){
        endSession.value = true  // Définis la fin de la séance
      }
    }
  }
}


function init(){
  countdown.value = 0
  endSession.value = false
  nbExercise.value = 1
}

// Timer
const duration = ref(1000) // Définit la valeur à 1 seconde
const countdown = ref(0)

let lastTime
let handle

// Le décompte du temps
const update = () => {
  countdown.value = duration.value - (performance.now() - lastTime)
  // Quand le temps est écoulé.
  if (countdown.value  <= 0) {
    cancelAnimationFrame(handle)
    restTime.value = false
    manageSession()
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

<template id="app">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <!--Ubuntu Police-->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap" rel="stylesheet">

  <div id="body" class="blue-theme-main">
    <h1 class="ubuntu-medium title mt-4">MMA</h1>
  </div>

  <div class="card boxes blue-theme-main-boxes" id="box-1" @click="messageNew(1)">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular fs-2">Force Poussée</h5>
      <p class="card-text ubuntu-light-italic fs-5">Lundi et Vendredi</p>
    </div>
  </div>

  <div class="card boxes blue-theme-main-boxes" @click="messageNew(2)">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular fs-2">Force Tirage</h5>
      <p class="card-text ubuntu-light-italic fs-5">Mercredi et Samedi</p>
    </div>
  </div>

  <div class="card boxes blue-theme-main-boxes" @click="messageNew(3)">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular fs-2">Jambes</h5>
      <p class="card-text ubuntu-light-italic fs-5">Jeudi</p>
    </div>
  </div>

  <div id="newPage" class="blue-theme-newPage" v-if="message !== ''">
    <!--Bouton pour fermer la page-->
    <button type="button" class="btn-close mt-3 ms-3" aria-label="Close" @click="messageNew(0)"></button>
    <h1 class="ubuntu-medium fs-1">Entrainement {{ message }}</h1>
    <div class="card mx-5 mt-5 blue-theme-newPage-boxes">
      <!--Le message de fin de série-->
      <div class="card-body" v-if="endSession">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <!--Les exercices-->
      <div class="card-body" v-else>
        <h5 class="card-title ubuntu-regular fs-3">{{ dictonnaryExercices[type][nbExercise].nom }} {{ dictonnaryExercices[type].alternate ? "en alternance" : "" }}</h5>
        <p class="card-text ubuntu-light-italic fs-5">{{ dictonnaryExercices[type][nbExercise].repetitions }} reps x {{ dictonnaryExercices[type][nbExercise].series }} séries, {{ dictonnaryExercices[type][nbExercise].recuperation }} secondes</p>
      </div>
    </div>
    <!--Bouton qui débute la session, affiche la section collapse-->
    <button type="button" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5" data-bs-toggle="collapse" data-bs-target="#instructions" aria-expanded="false" aria-controls="instructions" @click="init()">Début / Arrêt</button>

    <!--Les instructions-->
    <div class="collapse" id="instructions">
      <div v-if="!endSession" class="card card-body blue-theme-newPage-boxes mt-2 mx-5">
        <!--Les exercices-->
        <div v-if="!restTime">
          <!--Exercices-->
          <p class="card-title ubuntu-regular fs-3">Fait {{ dictonnaryExercices[type][nbExercise].repetitions }} répetitions</p>
          <p class="card-text ubuntu-light-italic fs-5">{{ dictonnaryExercices[type][nbExercise].conseil }}</p>
          <!--Changement étape-->
          <button type="button" class="btn btn-primary mt-4 fs-5 w-100" @click="next()">Suivant</button>
        </div>
        <div v-else>
          <!--Le temps de pause-->
          <label class="fs-4"
          >Temps de repos <progress :value="progressRate"></progress
          ></label>
          <div class="fs-5">{{ (countdown / 1000).toFixed(1) }}s</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
};
</script>

<style>
/* Pour la couleur: https://paletton.com/#uid=13i0u0ksnFdhuNAn1IHCVyOCHoW */

div {
  user-select: none;  /* Enlève la selection du texte dû au double clique */
}

#body {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

.title {
  padding-top: 30px;
  text-align: center;
  font-size: 50px;
}

.boxes {
  margin: 0 30px 70px 30px;
}

#box-1 {
  margin-top: 200px;
}


#newPage {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

#newPage h1 {
  text-align: center;
  padding-top: 30px;
}

/*Pour thème coloré (déclinaison de bleu)*/

/*La page principale*/
.blue-theme-main {
  background-color: #15baba;
}

.blue-theme-main-boxes {
  background-color: #00a2a2;
}

/*La page d'exercice*/
.blue-theme-newPage {
  background-color: #37c5c5;
}

.blue-theme-newPage-boxes {
  background-color: #60d4d4;
}


/*Font style from Google Font Ubuntu*/
.ubuntu-light {
  font-family: "Ubuntu", sans-serif;
  font-weight: 300;
  font-style: normal;
}

.ubuntu-regular {
  font-family: "Ubuntu", sans-serif;
  font-weight: 400;
  font-style: normal;
}

.ubuntu-medium {
  font-family: "Ubuntu", sans-serif;
  font-weight: 500;
  font-style: normal;
}

.ubuntu-light-italic {
  font-family: "Ubuntu", sans-serif;
  font-weight: 300;
  font-style: italic;
}

.ubuntu-regular-italic {
  font-family: "Ubuntu", sans-serif;
  font-weight: 400;
  font-style: italic;
}

.ubuntu-medium-italic {
  font-family: "Ubuntu", sans-serif;
  font-weight: 500;
  font-style: italic;
}

#app {
  background-color: #fff;
}

/*Bootstrap import*/
@import '~bootstrap/dist/css/bootstrap.css';

</style>
