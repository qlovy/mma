<script setup>
import { ref, onUnmounted, computed } from 'vue'
const message = ref('')
const exercisesBook = ref({
  poussee: {
    
    1: createExercise('Pompes Pikes', 6, 6, 60, 'Tu dois sentir tes épaules tout le long. Pense à avoir les avant-bras à 90 degrés du sol.'),
    2: createExercise('Pseudo pompes', 6, 6, 60, 'Main à 45 degrés. Sentir une pression sur les biceps et engager les abdos ainsi que les déltoïdes.'),
    alterne: true
  },
  tirage: {
    1: createExercise('Traction', 5, 6, 60, 'Rester bien droit, ne pas monter autour de la barre, sentir l\'engagement du dos. Utiliser les scapula pour débuter le mouvement.'),
    2: createExercise('Front lever row en négative', 5, 4, 60, 'Bras tendu, se mettre en postion haute (les pieds vers le ciel) puis ralentir le plus possible le mouvement.'),
    3: createExercise('Hanging', 1, "60 s", 60, 'Se laisser pendre au maximum sans douleur. Sentir un étirement au niveau des épaules principalement.'),
    4: createExercise('Dips', 4, 6, 60, 'Descendre un peu plus bas que l\'angle droit. Tendre les bras en position haute.'),
    alterne: false
  },
  jambes: {
    1: createExercise('Flexions plantaires', 4, 20, 30, 'Poser son pied sur une surface surélevée, sur une jambe. Le pied ne doit pas toucher le sol. Puis on monte le talon, faire levier'),
    2: createExercise('Split squat', 4, 10, 60, 'Un pied devant, un pied derrière. L\'espacement entre les deux pieds doit être assez large pour que quand on descend le genoux ne touche pas le sol.'),
    3: createExercise('Curl nordique', 4, 5, 60, 'Bloquer les pieds sous une surface. Descendre doucement vers l\'avant. Quand on peut plus on s\'aide des  mains. Puis on remonte.'),
    4: createExercise('Squat complet', 4, 10, 30, 'Pied à la largeur d\'épaules, reste en position basse environ une 1 seconde avant de remonter.'),
    5: createExercise('Chaise', 1, "60 s", 30, 'Dos contre un mur, les genoux à angle droit.'),
    alterne: false
  }
})
const serie = ref(0)
const type = ref("")
const restTime = ref(false)
const endSession = ref(false)
const nbExercise = ref(1)

const help = ref(false)

function createExercise(name, series, reps, rest_in_s, advice){
  return {nom: name, series: series, repetitions: reps, recuperation: rest_in_s, conseil: advice}
}

// Détermine le nombre d'exercices dans un thème donné
function howManyExercises(dictionnary){
  let array = Object.keys(dictionnary)  // Récupère toutes les clés de l'objet
  let final_array = array.filter((i) => !isNaN(parseInt(i)))  // Filtre pour ne ressortir qu'avec ceux qui se convertissent en nombre
  return final_array.length   // Renvoie la longueur du tableau soit le nombre d'exercices
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

function init(){
  countdown.value = 0
  endSession.value = false
  nbExercise.value = 1
  restTime.value = false
  serie.value = 0
}

function next() {
  restTime.value = true // Affichage du timer
  // Si l'option d'alternance est activée et qu'on a complété une série
  if (exercisesBook.value[type.value].alterne && serie.value % 2 === 0){
    //duration.value = exerciceBooks.value[type.value][nbExercise.value].recuperation * 500
  }else{
    //duration.value = exerciceBooks.value[type.value][nbExercise.value].recuperation * 1000
  }
  manageSession()
  if (!endSession.value){
    reset() // Lance le décompte
  }
}

function manageSession() {
  serie.value++
  if (exercisesBook.value[type.value].alterne){
    nbExercise.value === 1 ? nbExercise.value++ : nbExercise.value-- // Alternance entre les deux exercices
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie.value === exercisesBook.value[type.value][nbExercise.value].series * 2){
      serie.value = 0    // Reset le compteur de séries
      endSession.value = true  // Définis la fin de la séance
    }
  }else{
    // Si le nombre de séries faites correspondent à celles qui doivent être faites
    if (serie.value === exercisesBook.value[type.value][nbExercise.value].series){
      serie.value = 0    // Reset le compteur de séries
      // Si le nombre d'exercices faits correspondent au nombre d'exercices à faire
      if (nbExercise.value === howManyExercises(exercisesBook.value[type.value])){
        endSession.value = true  // Définis la fin de la séance
      }else{
        nbExercise.value++ // Compte le nombre d'exercices fait
      }
    }
  }
}

// Définis les informations à afficher en dessous du nom de l'exercice
function infoExercice(repetition, series, timeToRest){
  let s = (series === 1 ? " serie" : " series") // Change l'orthorgraphe en fonction du nombre affiché
  // Si repetition est une chaîne de caractère
  if (typeof repetition === "string"){
    return repetition + " x " + series + s + ", " + timeToRest + " secondes"
  }else{
    return repetition + " reps x " + series + s + ", " + timeToRest + " secondes"
  }
}

// Définis les instructions à afficher
function instructionExercice(repetition){
  if (typeof repetition === "string"){
    return "Tiens pendant " + repetition + "econdes"
  }else{
    return "Fait " + repetition + " répétions"
  }
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
    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">Entrainement {{ message }}</h1>
    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-newPage-boxes">
      <!--Le message de fin de série-->
      <div class="card-body" v-if="endSession">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <!--Les exercices-->
      <div class="card-body" v-else>
        <h5 class="card-title ubuntu-regular fs-3">{{ exercisesBook[type][nbExercise].nom }}{{ exercisesBook[type].alterne ? " en alternance" : "" }}</h5>
        <p class="card-text ubuntu-light-italic fs-5">{{ infoExercice(exercisesBook[type][nbExercise].repetitions, exercisesBook[type][nbExercise].series, exercisesBook[type][nbExercise].recuperation) }}</p>
      </div>
    </div>
    <!--Bouton qui débute la session, affiche la section collapse-->
    <button type="button" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5" data-bs-toggle="collapse" data-bs-target="#instructions" aria-expanded="false" aria-controls="instructions" @click="init()">Début / Arrêt</button>

    <!--Les instructions-->
    <div class="collapse" id="instructions">
      <div v-if="!endSession" class="card card-body blue-theme-newPage-boxes mt-2 mx-5">
        <div v-if="!restTime">
          <!--L'instruction-->
          <p class="card-title ubuntu-regular fs-3">{{ instructionExercice(exercisesBook[type][nbExercise].repetitions) }}</p>
          <!--Compteur de série-->
          <p class="card-text ubuntu-light-italic fs-5">Plus que {{ exercisesBook[type][nbExercise].series - serie }}{{ exercisesBook[type][nbExercise].series - serie === 1 ? " série" : " séries" }}</p>
          <!--Le conseil-->
          <p class="card-text ubuntu-light-italic fs-5">{{ exercisesBook[type][nbExercise].conseil }}</p>
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
          <button type="button" class="btn ubuntu-light fs-5" @click="nbExercise++; help=false">Passer un exercice</button>
        </div>
      </div>
    </Transition>
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

/* La page de chaque exercice*/
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

/*La page d'aide*/
#help-page {

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


/* Pour la transition */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

/*Bootstrap import*/
@import '~bootstrap/dist/css/bootstrap.css';

</style>
