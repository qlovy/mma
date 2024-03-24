<script setup>
import { ref, onUnmounted, computed } from 'vue'
const message = ref('')
const exercisesText = ref(
    [ [   ['Pompes Pikes surrélevées'], ['6 séries de 6 reps avec une minute de récupération'],
                ['Pseudo Pompes'], ['6 séries de 6 reps avec une minute de récupération'], ['Fait 6 pompes pikes'],
                ['Tu dois sentir tes épaules tout le long. Pense à avoir les avant-bras à 90 degrés du sol.'], ['Fait 6 pseudos pompes'],
                ['Main à 45 degrés. Sentir une pression sur les biceps et engager les abdos ainsi que les déltoïdes.']  ],
            [ [''], [''] ] ])
const part = ref(1)
const type = ref(-1)

function messageNew (origin){
  if (origin === 1) {
    message.value = 'Poussée'
    type.value = 0
  }else if (origin === 2){
    message.value = 'Tirage'
    type.value = 1
  }else if (origin === 3){
    message.value = 'Jambes'
    type.value = 2
  }else{
    message.value = ''
    type.value = -1
  }
}

function next(){
  part.value++
  reset() // Lance le décompte
}

function init(){
  elapsed.value = 0
  part.value = 1
}

// Timer
const duration = ref(1000) // Définit à 60 secondes
const elapsed = ref(0)

let lastTime
let handle

// Le décompte du temps
const update = () => {
  elapsed.value = duration.value - (performance.now() - lastTime)
  // Quand le temps est écoulé.
  if (elapsed.value  <= 0) {
    cancelAnimationFrame(handle)
    part.value++   // Change de page automatiquement.
  } else {
    handle = requestAnimationFrame(update)
  }
}

// La réinitialisation du timer
const reset = () => {
  elapsed.value = duration.value
  lastTime = performance.now()
  update()
}

// L'évolution de la barre
const progressRate = computed(() =>
    Math.min(elapsed.value / duration.value, 1)
)

onUnmounted(() => {
  cancelAnimationFrame(handle)
})
</script>

<template id="app">
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
    <button type="button" class="btn-close mt-3 ms-3" aria-label="Close" @click="messageNew(0)"></button>
    <h1 class="ubuntu-medium">Entrainement {{ message }}</h1>
    <!--Les exercices-->
    <div class="card mx-5 mt-5 blue-theme-newPage-boxes">
      <!--Le premier exercice-->
      <div class="card-body" v-if="part < 12">
        <h5 class="card-title ubuntu-regular">{{ exercisesText[type][0][0] }}</h5>
        <p class="card-text ubuntu-light-italic">{{ exercisesText[type][1][0] }}</p>
      </div>
      <!--Le deuxième exercice-->
      <div class="card-body" v-else-if="part < 24">
        <h5 class="card-title ubuntu-regular">{{ exercisesText[type][2][0] }}</h5>
        <p class="card-text ubuntu-light-italic">{{ exercisesText[type][3][0] }}</p>
      </div>
      <!--Le message de fin de série-->
      <div class="card-body" v-else>
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
    </div>
    <!--Bouton qui débute la session, affiche la section collapse-->
    <button type="button" class="btn btn-primary mt-4 mx-5" data-bs-toggle="collapse" data-bs-target="#instructions" aria-expanded="false" aria-controls="instructions" @click="init()">Début / Arrêt</button>
    <!--Les instructions-->
    <div class="collapse" id="instructions">
      <div v-if="part % 2 !== 0 && part < 24" class="card card-body blue-theme-newPage-boxes mt-4 mx-5">
        <!--Ex1-->
        <div v-if="part < 12">
          <p class="ubuntu-regular fs-4">{{ exercisesText[type][4][0] }}</p>
          <p class="ubuntu-light-italic fs-6">{{ exercisesText[type][5][0] }}</p>
        </div>
        <!--Ex2-->
        <div v-else>
          <p class="ubuntu-regular fs-4">{{ exercisesText[type][6][0] }}</p>
          <p class="ubuntu-light-italic fs-6">{{ exercisesText[type][7][0] }}</p>
        </div>
      </div>

      <div v-if="part % 2 === 0 && part < 24" class="card card-body mt-4 mx-5 blue-theme-newPage-boxes">
        <label
        >Rest Time: <progress :value="progressRate"></progress
        ></label>

        <div>{{ (elapsed / 1000).toFixed(1) }}s</div>
      </div>
      <!--Changement étape-->
      <button v-if="part % 2 === 1 && part < 24" type="button" class="btn btn-primary mt-4 mx-5" @click="next()">Suivant</button>
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
