<script setup>
import {ref, onUnmounted, computed} from 'vue'

// Les macros ou objets
import exerciseSet from './components/exerciseSet.vue'

const message = ref('') // ref est seulement pour enregistrer ou indiquer une référence à des éléments HTML ou à des éléments enfants dans le modèle de votre application.
let exercisesBook = [
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
const firstBox = ref()

function createExercise(name, series, reps, rest_in_s, advice) {
  return {nom: name, series: series, repetitions: reps, recuperation: rest_in_s, conseil: advice}
}

// Détermine le nombre d'exercices dans un thème donné
function howManyExercises(dictionnary) {
  let array = Object.keys(dictionnary)  // Récupère toutes les clés de l'objet
  let final_array = array.filter((i) => !isNaN(parseInt(i)))  // Filtre pour ne ressortir qu'avec ceux qui se convertissent en nombre (donc les 1, 2, 3, ...)
  return final_array.length   // Renvoie la longueur du tableau soit le nombre d'exercices
}

// Gère l'assignation de la valeur actuelle de référence
function manageActualUseRef() {
  // Si l'échauffement est activé
  if (warmup) {
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
  countdown.value = 0
  endSession.value = false
  nbExercise.value = 1
  restTime.value = false
  serie.value = 0
  warmup.value = true
}

// Gère le passage d'une série à une autre ou d'un exercice à un autre
function next() {
  restTime.value = true // Définis l'affichage du timer
  // Définis le temps de repos
  // Si l'échauffement est actif
  if (warmup) {
    duration.value = exercisesBook[type.value].echauffement[nbExercise.value].recuperation * 1000
  } else if (exercisesBook[type].alterne && serie % 2 === 0) {
    // Si l'option d'alternance est activée et qu'on a complété une série
    duration.value = exercisesBook[type.value][nbExercise.value].recuperation * 500  // le temps est réduit de moitié
  } else {
    duration.value = exercisesBook[type.value][nbExercise.value].recuperation * 1000
  }
  // Actualise la session
  manageSession()
  manageActualUseRef()
  //  Si la session n'est pas finie
  if (!endSession) {
    reset() // Lance le décompte
  }
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
    if (exercisesBook.value[type.value].alterne) {
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

// Définis les informations à afficher en dessous du nom de l'exercice
function infoExercice(repetition, series, timeToRest) {
  let s = (series === 1 ? " serie" : " series") // Change l'orthorgraphe en fonction du nombre affiché
  // Si repetition est une chaîne de caractère
  if (typeof repetition === "string") {
    return repetition + " x " + series + s + ", " + timeToRest + " secondes"
  } else {
    return repetition + " reps x " + series + s + ", " + timeToRest + " secondes"
  }
}

// Définis les instructions à afficher
function instructionExercice(repetition) {
  // Si le paramètre repetition contient une chaîne de caractère
  if (typeof repetition === "string") {
    // Le chiffre contenu dans la chaine de caractère est plus petit que 10
    if (parseInt(repetition.slice(0, 2)) > 10) {
      timeExercise.value = true // Définis l'affichage du temps d'exercice
    }
    return "Tiens pendant " + repetition + "econdes"
  } else {
    timeExercise.value = false  // Fait disparaître l'affichage du temps d'exercice
    return "Fait " + repetition + " répétions"
  }
}

// Lance un timer pour un exercice
function launchExerciseTimer(repetition) {
  duration.value = parseInt(repetition.slice(0, 2)) * 1000
  reset()
}

// Aide
function skipExercise() {
  nbExercise.value++
  help.value = false
  next()
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
  if (countdown.value <= 0) {
    countdown.value = 0
    cancelAnimationFrame(handle)
    restTime.value = false
    if (timeExercise.value === true) {
      next()
      timeExercise.value = false
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

<template id="app">
  <!--Permet aux bandeaux de fond (haut et bas sur Iphone) d'être la même couleur que le body-->
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <!--Ubuntu Police-->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link
      href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap"
      rel="stylesheet">

  <exercise-set :array="exercisesBook" @func="messageNew"/>


  <div id="exerciseDetails" class="blue-theme-newPage" v-if="exercisePage">
    <!--Bouton pour fermer la page-->
    <button type="button" class="btn-close mt-3 ms-3" aria-label="Close" @click="exercisePage = false"></button>
    <!--Titre de l'entrainement-->
    <h1 class="ubuntu-medium fs-1">{{ message }}</h1>
    <!--Annonce du nom de l'exercice à faire-->
    <div class="card mx-5 mt-5 blue-theme-newPage-boxes">
      <!--Le message de fin de série-->
      <div class="card-body" v-if="endSession">
        <h5 class="card-title ubuntu-regular">Bravo !</h5>
      </div>
      <!--Les exercices-->
      <div class="card-body" v-else>
        <h5 class="card-title ubuntu-regular fs-3">{{
            actualUseRef.nom
          }}{{ !warmup && exercisesBook[type].alterne ? " en alternance" : "" }}</h5>
        <p class="card-text ubuntu-light-italic fs-5">
          {{ infoExercice(actualUseRef.repetitions, actualUseRef.series, actualUseRef.recuperation) }}</p>
      </div>
    </div>
    <!--Bouton qui débute la session, affiche la section collapse-->
    <button type="button" class="btn btn-primary mt-4 ms-5 fs-5 w-75 mb-5" data-bs-toggle="collapse"
            data-bs-target="#instructions" aria-expanded="false" aria-controls="instructions" @click="init()">Début /
      Arrêt
    </button>

    <!--Les instructions-->
    <div class="collapse" id="instructions">
      <div v-if="!endSession" class="card card-body blue-theme-newPage-boxes mt-2 mx-5">
        <div v-if="!restTime">
          <!--L'instruction-->
          <p class="card-title ubuntu-regular fs-3">{{ instructionExercice(actualUseRef.repetitions) }}</p>
          <div v-if="timeExercise">
            <label class="fs-4"
            >Temps d'exercice
              <progress :value="progressRate"></progress
              >
            </label>
            <div class="fs-5">{{ (countdown / 1000).toFixed(1) }}s</div>
            <button type="button" class="btn btn-primary mb-2 mt-2"
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
          <button type="button" class="btn btn-primary mt-2 fs-5 w-100" @click="next()" v-if="!timeExercise">Suivant
          </button>
        </div>
        <div v-else>
          <!--Le temps de pause-->
          <label class="fs-4"
          >Temps de repos
            <progress :value="progressRate"></progress
            >
          </label>
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
          <button type="button" class="btn ubuntu-light fs-5" @click="skipExercise()">Passer un exercice</button>
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
  user-select: none; /* Enlève la selection du texte dû au double clique */
}

/*La page d'exercice*/
.blue-theme-newPage {
  background-color: #37c5c5;
}

.blue-theme-newPage-boxes {
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
