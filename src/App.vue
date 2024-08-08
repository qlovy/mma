<script setup>
import {reactive, ref} from 'vue'

// Les composants
import exerciseList from './components/exerciseList.vue'
import exerciseDetails from './components/exerciseDetails.vue'

const message = ref('') // ref est seulement pour enregistrer ou indiquer une référence à des éléments HTML ou à des éléments enfants dans le modèle de votre application.
const exercisesBook = [
  {
    nom: 'Force Poussée',
    jour: 'Lundi et Vendredi',
    echauffements: [
      createExercise('Tourner les poignets', 1, '30 s', 1, 'Ne pas aller trop fort. Sentir que sa chauffe.'),
      createExercise('Assouplissement des poignets', 1, '30 s', 1, 'Les mains au sol faire des cercles avec les bras, changer la position des mains.'),
      createExercise('Tourner les coudes', 1, '30 s', 1, 'Tourner les coudes dans le sens-horaire et anti-horaire. Aller doucement.'),
      createExercise('Tourner les épaules', 1, '30 s', 1, 'Faire des grands cercles en avant et en arrière. Aller doucement.')
    ],
    exercices: [
      createExercise('Pompes Pikes', 6, 6, 60, 'Tu dois sentir tes épaules tout le long. Pense à avoir les avant-bras à 90 degrés du sol.'),
      createExercise('Pseudo pompes', 6, 6, 60, 'Main à 45 degrés. Sentir une pression sur les biceps et engager les abdos ainsi que les déltoïdes.')
    ],
    alterne: true
  },
  {
    nom: 'Force Tirage',
    jour: 'Mercredi et Samedi',
    echauffements: [createExercise('Tourner les coudes', 1, '30 s', 1, 'Tourner les coudes dans le sens-horaire et anti-horaire. Aller doucement.'),
      createExercise('Tourner les épaules', 1, '30 s', 1, 'Faire des grands cercles en avant et en arrière. Aller doucement.'),
      createExercise('Burpies', 1, 10, 1, 'Bien faire le mouvement.')
    ],
    exercices: [
      createExercise('Traction', 5, 6, 60, 'Rester bien droit, ne pas monter autour de la barre, sentir l\'engagement du dos. Utiliser les scapula pour débuter le mouvement.'),
      createExercise('Front lever row en négative', 5, 4, 60, 'Bras tendu, se mettre en postion haute (les pieds vers le ciel) puis ralentir le plus possible le mouvement.'),
      createExercise('Hanging', 1, "60 s", 60, 'Se laisser pendre au maximum sans douleur. Sentir un étirement au niveau des épaules principalement.'),
      createExercise('Dips', 4, 6, 60, 'Descendre un peu plus bas que l\'angle droit. Tendre les bras en position haute.')
    ],
    alterne: false
  },
  {
    nom: 'Jambes',
    jour: 'Jeudi',
    echauffements: [
      createExercise('Jeu du parcours', 1, '60 s', 1, 'Courir sur place avec des sauts à intervalles irréguliers')
    ],
    exercices: [
      createExercise('Flexions plantaires', 4, 20, 30, 'Poser son pied sur une surface surélevée, sur une jambe. Le pied ne doit pas toucher le sol. Puis on monte le talon, faire levier'),
      createExercise('Split squat', 4, 10, 60, 'Un pied devant, un pied derrière. L\'espacement entre les deux pieds doit être assez large pour que quand on descend le genoux ne touche pas le sol.'),
      createExercise('Curl nordique', 4, 5, 60, 'Bloquer les pieds sous une surface. Descendre doucement vers l\'avant. Quand on peut plus on s\'aide des  mains. Puis on remonte.'),
      createExercise('Squat complet', 4, 10, 30, 'Pied à la largeur d\'épaules, reste en position basse environ une 1 seconde avant de remonter.'),
      createExercise('Chaise', 1, "60 s", 30, 'Dos contre un mur, les genoux à angle droit.')
    ],
    alterne: false
  }
]

// L'object qui contient le contexte de fonctionnement interne
const ctx = reactive({
  serie: 0,
  type: 0,
  restTime: false,
  endSession: false,
  warmup: false,
  indexExercise: 0,
  actualUseRef: exercisesBook[0].echauffements[0],
  help: false,
  timeExercise: false,
  exercisePage: false,
  exercisesBook: exercisesBook
})

let exerciseArea = ref(true)

// Renvoie les informations relatives à un exercice sous forme d'objet
function createExercise(name, series, reps, rest_in_s, advice) {
  return {nom: name, series: series, repetitions: reps, recuperation: rest_in_s, conseil: advice}
}

// Gère l'assignation de la valeur actuelle de référence
function manageActualUseRef() {
  // Si l'échauffement est activé
  if (ctx.warmup) {
    // Permet d'éviter une "TypeError" si une option de l'objet n'existe pas.
    try {
      // S'il n'y a pas d'erreur
      ctx.actualUseRef = exercisesBook[ctx.type].echauffements[ctx.indexExercise] // La page de l'échauffement
    } catch (TypeError) {
      // En cas de "TypeError" (due à une valeur undefined)
      ctx.actualUseRef = exercisesBook[0].echauffements[0] // Valeur tampon
    }
  } else {
    try {
      ctx.actualUseRef = exercisesBook[ctx.type].exercices[ctx.indexExercise] // La page de l'exercice
    } catch (TypeError) {
      ctx.actualUseRef = exercisesBook[0].echauffements[0]
    }
  }
}

// Gère le choix de l'exercice
function messageNew(index) {
  ctx.type = index  // Défini le type en fonction de l'exercice choisi
  message.value = 'Entrainement ' + exercisesBook[index].nom  // Message de bienvenue
  // Initialise l'échauffement
  ctx.exercisePage = true
  ctx.warmup = true
  manageActualUseRef()
}

// Demande la permission d'envoyé des notifications
Notification.requestPermission().then();

// Gère le menu
function exercisePage(){

}

function settingsPage(){

}
</script>

<template id="app">
  <!--Permet aux bandeaux de fond (haut et bas sur Iphone) d'être la même couleur que le body-->
  <meta content="black-translucent" name="apple-mobile-web-app-status-bar-style">
  <!--Ubuntu Police-->
  <link href="https://fonts.googleapis.com" rel="preconnect">
  <link crossorigin href="https://fonts.gstatic.com" rel="preconnect">
  <link
      href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap"
      rel="stylesheet">
  <div id="body">
    <!--La partie Exercices-->
    <div v-if="exerciseArea">
      <!--La liste des exercices possibles-->
      <exerciseList v-if="!ctx.exercisePage" :array="exercisesBook" @func="messageNew"/>

      <!--La page d'utlistation des exercices-->
      <exerciseDetails v-else id="exerciseDetails"
                       :message="message" :ctx="ctx"
                       @manage-actual-use-ref="manageActualUseRef"/>
    </div>

    <!--La partie Réglages-->
    <div v-else>
      <p>Your a in my world now !!</p>
    </div>



    <!--Le menu pour basculer entre la liste d'exercice ou alors les paramètres-->
    <!--Utilisation table cells -->
    <table id="menu">
      <tbody>
        <tr>
          <td class="align-middle" @click="exerciseArea = true">
            <!--Icon des exercices-->
            <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor" class="bi bi-activity icon-menu" viewBox="0 0 16 16">
              <path fill-rule="evenodd" d="M6 2a.5.5 0 0 1 .47.33L10 12.036l1.53-4.208A.5.5 0 0 1 12 7.5h3.5a.5.5 0 0 1 0 1h-3.15l-1.88 5.17a.5.5 0 0 1-.94 0L6 3.964 4.47 8.171A.5.5 0 0 1 4 8.5H.5a.5.5 0 0 1 0-1h3.15l1.88-5.17A.5.5 0 0 1 6 2"/>
            </svg>
          </td>

          <td class="align-middle" @click="exerciseArea = false">
            <!-- Icon des paramètres-->
            <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor" class="bi bi-gear icon-menu" viewBox="0 0 16 16">
              <path d="M8 4.754a3.246 3.246 0 1 0 0 6.492 3.246 3.246 0 0 0 0-6.492M5.754 8a2.246 2.246 0 1 1 4.492 0 2.246 2.246 0 0 1-4.492 0"/>
              <path d="M9.796 1.343c-.527-1.79-3.065-1.79-3.592 0l-.094.319a.873.873 0 0 1-1.255.52l-.292-.16c-1.64-.892-3.433.902-2.54 2.541l.159.292a.873.873 0 0 1-.52 1.255l-.319.094c-1.79.527-1.79 3.065 0 3.592l.319.094a.873.873 0 0 1 .52 1.255l-.16.292c-.892 1.64.901 3.434 2.541 2.54l.292-.159a.873.873 0 0 1 1.255.52l.094.319c.527 1.79 3.065 1.79 3.592 0l.094-.319a.873.873 0 0 1 1.255-.52l.292.16c1.64.893 3.434-.902 2.54-2.541l-.159-.292a.873.873 0 0 1 .52-1.255l.319-.094c1.79-.527 1.79-3.065 0-3.592l-.319-.094a.873.873 0 0 1-.52-1.255l.16-.292c.893-1.64-.902-3.433-2.541-2.54l-.292.159a.873.873 0 0 1-1.255-.52zm-2.633.283c.246-.835 1.428-.835 1.674 0l.094.319a1.873 1.873 0 0 0 2.693 1.115l.291-.16c.764-.415 1.6.42 1.184 1.185l-.159.292a1.873 1.873 0 0 0 1.116 2.692l.318.094c.835.246.835 1.428 0 1.674l-.319.094a1.873 1.873 0 0 0-1.115 2.693l.16.291c.415.764-.42 1.6-1.185 1.184l-.291-.159a1.873 1.873 0 0 0-2.693 1.116l-.094.318c-.246.835-1.428.835-1.674 0l-.094-.319a1.873 1.873 0 0 0-2.692-1.115l-.292.16c-.764.415-1.6-.42-1.184-1.185l.159-.291A1.873 1.873 0 0 0 1.945 8.93l-.319-.094c-.835-.246-.835-1.428 0-1.674l.319-.094A1.873 1.873 0 0 0 3.06 4.377l-.16-.292c-.415-.764.42-1.6 1.185-1.184l.292.159a1.873 1.873 0 0 0 2.692-1.115z"/>
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
  user-select: none; /* Enlève la selection du texte dû au double clique */
}

/*Le menu*/
#menu {
  background-color: #007777;
  position: absolute;
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