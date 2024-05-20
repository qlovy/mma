<script setup>
import {reactive, ref} from 'vue'

// Les macros ou objets
import exerciseList from './components/exerciseList.vue'
import exerciseDetails from './components/exerciseDetails.vue'

const message = ref('') // ref est seulement pour enregistrer ou indiquer une référence à des éléments HTML ou à des éléments enfants dans le modèle de votre application.
const exercisesBook = [
  {
    nom: 'Force Poussée',
    jour: 'Lundi et Vendredi',
    echauffement: {
      1: createExercise('Tourner les poignets', 1, '30 s', 1, 'Ne pas aller trop fort. Sentir que sa chauffe.'),
      2: createExercise('Assouplissement des poignets', 1, '30 s', 1, 'Les mains au sol faire des cercles avec les bras, changer la position des mains.'),
      3: createExercise('Tourner les coudes', 1, '30 s', 1, 'Tourner les coudes dans le sens-horaire et anti-horaire. Aller doucement.'),
      4: createExercise('Tourner les épaules', 1, '30 s', 1, 'Faire des grands cercles en avant et en arrière. Aller doucement.')
    },
    1: createExercise('Pompes Pikes', 6, 6, 60, 'Tu dois sentir tes épaules tout le long. Pense à avoir les avant-bras à 90 degrés du sol.'),
    2: createExercise('Pseudo pompes', 6, 6, 60, 'Main à 45 degrés. Sentir une pression sur les biceps et engager les abdos ainsi que les déltoïdes.'),
    alterne: true
  },
  {
    nom: 'Force Tirage',
    jour: 'Mercredi et Samedi',
    echauffement: {
      1: createExercise('Tourner les coudes', 1, '30 s', 1, 'Tourner les coudes dans le sens-horaire et anti-horaire. Aller doucement.'),
      2: createExercise('Tourner les épaules', 1, '30 s', 1, 'Faire des grands cercles en avant et en arrière. Aller doucement.'),
      3: createExercise('Burpies', 1, 10, 1, 'Bien faire le mouvement.')
    },
    1: createExercise('Traction', 5, 6, 60, 'Rester bien droit, ne pas monter autour de la barre, sentir l\'engagement du dos. Utiliser les scapula pour débuter le mouvement.'),
    2: createExercise('Front lever row en négative', 5, 4, 60, 'Bras tendu, se mettre en postion haute (les pieds vers le ciel) puis ralentir le plus possible le mouvement.'),
    3: createExercise('Hanging', 1, "60 s", 60, 'Se laisser pendre au maximum sans douleur. Sentir un étirement au niveau des épaules principalement.'),
    4: createExercise('Dips', 4, 6, 60, 'Descendre un peu plus bas que l\'angle droit. Tendre les bras en position haute.'),
    alterne: false
  },
  {
    nom: 'Jambes',
    jour: 'Jeudi',
    echauffement: {
      1: createExercise('Jeu du parcours', 1, '60 s', 1, 'Courir sur place avec des sauts à intervalles irréguliers')
    },
    1: createExercise('Flexions plantaires', 4, 20, 30, 'Poser son pied sur une surface surélevée, sur une jambe. Le pied ne doit pas toucher le sol. Puis on monte le talon, faire levier'),
    2: createExercise('Split squat', 4, 10, 60, 'Un pied devant, un pied derrière. L\'espacement entre les deux pieds doit être assez large pour que quand on descend le genoux ne touche pas le sol.'),
    3: createExercise('Curl nordique', 4, 5, 60, 'Bloquer les pieds sous une surface. Descendre doucement vers l\'avant. Quand on peut plus on s\'aide des  mains. Puis on remonte.'),
    4: createExercise('Squat complet', 4, 10, 30, 'Pied à la largeur d\'épaules, reste en position basse environ une 1 seconde avant de remonter.'),
    5: createExercise('Chaise', 1, "60 s", 30, 'Dos contre un mur, les genoux à angle droit.'),
    alterne: false
  }
]

const context = {
  serie: 0,
  type: 0,
  restTime: false,
  endSession: false,
  warmup: false,
  nbExercise: 0,
  actualUseRef: exercisesBook[0].echauffement["1"],
  help: false,
  timeExercise: false,
  exercisePage: false,
  exercisesBook: exercisesBook
}

/*
const serie = ref(0)
const type = ref(0)
const restTime = ref(false)
const endSession = ref(false)
const warmup = ref(false)
const nbExercise = ref(1)
const actualUseRef = ref(exercisesBook[0].echauffement["1"])
const help = ref(false)
const timeExercise = ref(false)
const exercisePage = ref(false) // Nécessaire d'utiliser un ref quand on l'utilise dans le html (par exemple dans un v-if)
*/
  
function createExercise(name, series, reps, rest_in_s, advice) {
  return {nom: name, series: series, repetitions: reps, recuperation: rest_in_s, conseil: advice}
}

// Gère l'assignation de la valeur actuelle de référence
function manageActualUseRef() {
  console.log(warmup.value)
  console.log(nbExercise.value)
  // Si l'échauffement est activé
  if (warmup.value) {
    // Permet d'éviter une "TypeError" si une option de l'objet n'existe pas.
    try {
      // S'il n'y a pas d'erreur
      actualUseRef.value = exercisesBook[type.value].echauffement[nbExercise.value] // La page de l'échauffement
    } catch (TypeError) {
      // En cas de "TypeError" (due à une valeur undefined)
      actualUseRef.value = exercisesBook[0].echauffement["1"] // Valeur tampon
    }
  } else {
    try {
      actualUseRef.value = exercisesBook[type.value][nbExercise.value] // La page de l'exercice
    } catch (TypeError) {
      actualUseRef.value = exercisesBook[0].echauffement["1"]
    }
  }
}

// Gère le choix de l'exercice
function messageNew(index) {
  for (let i = 0; i < exercisesBook.length; i++) {
    if (index === i) {
      type.value = index
      message.value = 'Entrainement ' + exercisesBook[index].nom
      // Initialise l'échauffement
      exercisePage.value = true
      warmup.value = true
      manageActualUseRef()
      break
    }
  }
}

// Initialise le début d'une session
function init() {
  endSession.value = false
  nbExercise.value = 1
  restTime.value = false
  serie.value = 0
  warmup.value = true
  manageActualUseRef()
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

  <exercise-list v-if="!exercisePage" id="body" :array="exercisesBook" @func="messageNew"/>

  <exercise-details v-else id="exerciseDetails" :actual-use-ref="actualUseRef"
                    :exercises-book="exercisesBook"
                    :message="message" :modify="reactive({serie: serie, nbExercise: nbExercise, warmup: warmup, restTime: restTime, endSession: endSession})"
                    :type="type"
                    @close="exercisePage = false" @init="init()" @manage-actual-use-ref="manageActualUseRef"/>

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

/*La page d'exercice*/
.blue-theme {
  background-color: #37c5c5;
}

.blue-theme-boxes {
  background-color: #60d4d4;
}

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
