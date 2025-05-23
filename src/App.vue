<script setup>
import {onMounted, ref} from 'vue'

// Les composants
import exerciseList from './components/exerciseList.vue'
import exerciseDetails from './components/exerciseDetails.vue'

let exercisesBook = ""

// Variables réactives
const data = ref(null)
const loading = ref(true)

// À la création
onMounted( () => {
  // Récupère le fichier JSON à l'url donnée, fonction callback, on attend sur elle
  fetch("https://raw.githubusercontent.com/wiki/qlovy/mma/programme.json")
      .then(response => {
        if (!response.ok) {
          throw new Error(`HTTP error ! status: ${response.status}`)
        }
        return response.json()
      })
      .then(json => {
        data.value = json
        exercisesBook = data.value
      })
      .catch(err => {
        console.log(err.message)
      })
      .finally(() => {
        loading.value = false
      })
})

// Permet d'afficher le programme de l'utilisateur (stocké dans le local storage) à la place du programme par défaut
updateDB()

// Actualise le livre d'exercices
function updateDB() {
  // Si l'utilisateur a chargé son programme
  if (localStorage.getItem("data") != null) {
    exercisesBook = JSON.parse(localStorage.getItem("data"))
    // Sinon, utilisation de celui par défaut.
  } else {
    exercisesBook = data.value
  }
}

let currentTraining = 0;
// Utilisation de ref() pour permettre une synchronisation de la variable entre les différents composants
const exercisePage = ref(false);

let exerciseArea = ref(true)

// Gère le choix de l'exercice
function messageNew(index) {
  exercisePage.value = true   // Active la page d'exercice
  currentTraining = exercisesBook[index]  // Stockage de l'entrainement choisi
}

// Pour fermer la page de l'exercice
function closeExercisePage() {
  exercisePage.value = false
}

// Demande la permission d'envoyer des notifications
Notification.requestPermission().then()

// Gestion du fichier JSON, pour l'ajout d'un fichier personnel à l'utilisateur
function handleJSON(event) {
  const file = event.target.files[0]  // Chargement du fichier .json sélectionné par l'utilisateur
  // S'il contient quelque chose
  if (file) {
    const reader = new FileReader()  // Création d'un lecteur
    reader.onload = function (e) {
      const jsonData = e.target.result  // Stockage du contenu du fichier
      try {
        localStorage.setItem("data", jsonData)  // Stockage dans le stockage du navigateur (spécifique à l'utilisateur et à l'appareil)
        updateDB()
        // En cas de problème
      } catch (error) {
        console.error('Erreur de parsing du JSON:', error)
      }
    }
    reader.readAsText(file)
  } else {
    console.log('Aucun fichier sélectionné')
  }
}

// Retour au programme d'origine
function resetDefaultProgramme() {
  localStorage.clear()
  updateDB()
}

</script>

<template id="app">
  <!--Ubuntu Police-->
  <link href="https://fonts.googleapis.com" rel="preconnect">
  <link crossorigin href="https://fonts.gstatic.com" rel="preconnect">
  <link
      href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap"
      rel="stylesheet">
  <div v-if="loading"><h1>Loading...</h1></div>
  <div id="body" v-else>

    <!--La partie Exercices-->
    <div v-if="exerciseArea">
      <!--La liste des exercices possibles-->
      <exerciseList v-if="!exercisePage" :array="exercisesBook" @func="messageNew" class="overflow-auto"/>

      <!--La page de l'exercice choisi-->
      <exerciseDetails v-else id="exerciseDetails"
                       :currentTraining="currentTraining" @close="closeExercisePage"/>
    </div>

    <!--La partie Réglages-->
    <div v-else class="overflow-auto">
      <h1 class="ubuntu-medium mt-3 d-flex justify-content-center display-2">Réglages</h1>
      <!--Tous les réglages-->
      <div class="ms-2">
        <!--Gestion de l'apparence-->
        <h3 class="ubuntu-regular mt-3">Apparence</h3>
        <p class="ubuntu-light-italic">Faire choisir l'utilisateur entre du blanc , couleur (bleu), noir</p>
        <!--Les téléchargements pour créer le fichier JSON de l'utilisateur-->
        <h3 class="ubuntu-regular mt-3">Télécharge le modèle JSON</h3>
        <p class="ubuntu-light">Pour pouvoir configurer des exercices, il te faut remplir ce fichier JSON.</p>
        <a href="./data/model.json" class="me-4" download="model">Le modèle</a>
        <a href="./data/tutorial.md" download="tuto">Le tutoriel</a>
        <!--L'importation du fichier JSON-->
        <h3 class="ubuntu-regular mt-3">Importe ton fichier JSON</h3>
        <p class="ubuntu-light">Pour configurer tes exercices, tu peux importer ton fichier .json en le séléctionnant
          sur ton appareil.<br>Attention, ton fichier .json doit être du même format que celui que tu peux télécharger
          (ci-dessus)!</p>
        <!--La fenêtre de dialogue pour importer le fichier JSON-->
        <input type="file" accept=".json" placeholder="hello" @change="handleJSON">

        <!--Pour retourner au programme par défaut-->
        <h3 class="ubuntu-regular mt-3">Retour au programme par défaut</h3>
        <p class="ubuntu-light">En cliquant sur le bouton ci-dessous, tu retourneras au programme par défaut.</p>
        <button class="btn btn-primary" @click="resetDefaultProgramme">Retour au programme par défaut</button>
      </div>

    </div>


    <!--Le menu pour basculer entre la liste d'exercice ou alors les paramètres-->
    <!--Utilisation table cells pour placer les icones de manière simple-->
    <table id="menu" v-if="!exercisePage">
      <tbody>
      <tr>
        <td class="align-middle" @click="exerciseArea = true">
          <!--Icon des exercices-->
          <svg v-if="exerciseArea" xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="white"
               class="bi bi-activity icon-menu" viewBox="0 0 16 16">
            <path fill-rule="evenodd"
                  d="M6 2a.5.5 0 0 1 .47.33L10 12.036l1.53-4.208A.5.5 0 0 1 12 7.5h3.5a.5.5 0 0 1 0 1h-3.15l-1.88 5.17a.5.5 0 0 1-.94 0L6 3.964 4.47 8.171A.5.5 0 0 1 4 8.5H.5a.5.5 0 0 1 0-1h3.15l1.88-5.17A.5.5 0 0 1 6 2"/>
          </svg>
          <svg v-else xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor"
               class="bi bi-activity icon-menu" viewBox="0 0 16 16">
            <path fill-rule="evenodd"
                  d="M6 2a.5.5 0 0 1 .47.33L10 12.036l1.53-4.208A.5.5 0 0 1 12 7.5h3.5a.5.5 0 0 1 0 1h-3.15l-1.88 5.17a.5.5 0 0 1-.94 0L6 3.964 4.47 8.171A.5.5 0 0 1 4 8.5H.5a.5.5 0 0 1 0-1h3.15l1.88-5.17A.5.5 0 0 1 6 2"/>
          </svg>
        </td>

        <td class="align-middle" @click="exerciseArea = false">
          <!-- Icon des paramètres-->
          <svg v-if="!exerciseArea" xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="white"
               class="bi bi-gear icon-menu" viewBox="0 0 16 16">
            <path
                d="M8 4.754a3.246 3.246 0 1 0 0 6.492 3.246 3.246 0 0 0 0-6.492M5.754 8a2.246 2.246 0 1 1 4.492 0 2.246 2.246 0 0 1-4.492 0"/>
            <path
                d="M9.796 1.343c-.527-1.79-3.065-1.79-3.592 0l-.094.319a.873.873 0 0 1-1.255.52l-.292-.16c-1.64-.892-3.433.902-2.54 2.541l.159.292a.873.873 0 0 1-.52 1.255l-.319.094c-1.79.527-1.79 3.065 0 3.592l.319.094a.873.873 0 0 1 .52 1.255l-.16.292c-.892 1.64.901 3.434 2.541 2.54l.292-.159a.873.873 0 0 1 1.255.52l.094.319c.527 1.79 3.065 1.79 3.592 0l.094-.319a.873.873 0 0 1 1.255-.52l.292.16c1.64.893 3.434-.902 2.54-2.541l-.159-.292a.873.873 0 0 1 .52-1.255l.319-.094c1.79-.527 1.79-3.065 0-3.592l-.319-.094a.873.873 0 0 1-.52-1.255l.16-.292c.893-1.64-.902-3.433-2.541-2.54l-.292.159a.873.873 0 0 1-1.255-.52zm-2.633.283c.246-.835 1.428-.835 1.674 0l.094.319a1.873 1.873 0 0 0 2.693 1.115l.291-.16c.764-.415 1.6.42 1.184 1.185l-.159.292a1.873 1.873 0 0 0 1.116 2.692l.318.094c.835.246.835 1.428 0 1.674l-.319.094a1.873 1.873 0 0 0-1.115 2.693l.16.291c.415.764-.42 1.6-1.185 1.184l-.291-.159a1.873 1.873 0 0 0-2.693 1.116l-.094.318c-.246.835-1.428.835-1.674 0l-.094-.319a1.873 1.873 0 0 0-2.692-1.115l-.292.16c-.764.415-1.6-.42-1.184-1.185l.159-.291A1.873 1.873 0 0 0 1.945 8.93l-.319-.094c-.835-.246-.835-1.428 0-1.674l.319-.094A1.873 1.873 0 0 0 3.06 4.377l-.16-.292c-.415-.764.42-1.6 1.185-1.184l.292.159a1.873 1.873 0 0 0 2.692-1.115z"/>
          </svg>
          <svg v-else xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor"
               class="bi bi-gear icon-menu" viewBox="0 0 16 16">
            <path
                d="M8 4.754a3.246 3.246 0 1 0 0 6.492 3.246 3.246 0 0 0 0-6.492M5.754 8a2.246 2.246 0 1 1 4.492 0 2.246 2.246 0 0 1-4.492 0"/>
            <path
                d="M9.796 1.343c-.527-1.79-3.065-1.79-3.592 0l-.094.319a.873.873 0 0 1-1.255.52l-.292-.16c-1.64-.892-3.433.902-2.54 2.541l.159.292a.873.873 0 0 1-.52 1.255l-.319.094c-1.79.527-1.79 3.065 0 3.592l.319.094a.873.873 0 0 1 .52 1.255l-.16.292c-.892 1.64.901 3.434 2.541 2.54l.292-.159a.873.873 0 0 1 1.255.52l.094.319c.527 1.79 3.065 1.79 3.592 0l.094-.319a.873.873 0 0 1 1.255-.52l.292.16c1.64.893 3.434-.902 2.54-2.541l-.159-.292a.873.873 0 0 1 .52-1.255l.319-.094c1.79-.527 1.79-3.065 0-3.592l-.319-.094a.873.873 0 0 1-.52-1.255l.16-.292c.893-1.64-.902-3.433-2.541-2.54l-.292.159a.873.873 0 0 1-1.255-.52zm-2.633.283c.246-.835 1.428-.835 1.674 0l.094.319a1.873 1.873 0 0 0 2.693 1.115l.291-.16c.764-.415 1.6.42 1.184 1.185l-.159.292a1.873 1.873 0 0 0 1.116 2.692l.318.094c.835.246.835 1.428 0 1.674l-.319.094a1.873 1.873 0 0 0-1.115 2.693l.16.291c.415.764-.42 1.6-1.185 1.184l-.291-.159a1.873 1.873 0 0 0-2.693 1.116l-.094.318c-.246.835-1.428.835-1.674 0l-.094-.319a1.873 1.873 0 0 0-2.692-1.115l-.292.16c-.764.415-1.6-.42-1.184-1.185l.159-.291A1.873 1.873 0 0 0 1.945 8.93l-.319-.094c-.835-.246-.835-1.428 0-1.674l.319-.094A1.873 1.873 0 0 0 3.06 4.377l-.16-.292c-.415-.764.42-1.6 1.185-1.184l.292.159a1.873 1.873 0 0 0 2.692-1.115z"/>
          </svg>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "App",
};
</script>

<style>
/* Pour la couleur: https://paletton.com/#uid=13i0u0ksnFdhuNAn1IHCVyOCHoW */

#body {
  background-color: #15baba; /* Pour forcer la couleur du fond sur Iphone */
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

div {
  user-select: none; /* Enlève la selection du texte dû au double clique*/
  touch-action: manipulation; /* Enlève le zoom lors d'un double touché*/
}

/*Le menu*/
#menu {
  background-color: #007777;
  position: fixed;
  bottom: 0;
  width: 100%;
  height: 100px;
}

.icon-menu {
  color: #60d5d5;
  margin-left: 70px;
}

/*La page des paramètres*/

/* La page de chaque exercice*/
#exerciseDetails {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

#exerciseDetails h1 {
  text-align: center;
  padding-top: 30px;
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

/* Just in case of...
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
*/

#app {
  background-color: #fff;
}

/*Bootstrap import*/
@import '~bootstrap/dist/css/bootstrap.css';
</style>
