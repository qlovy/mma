<script setup>
const props = defineProps({
  data: Array
})

// Définis les informations à afficher en dessous du nom de l'exercice
function infoExercise(repetition, series, timeToRest) {
  // Change l'orthographe en fonction du nombre a affiché
  let typoSerie = (series === 1 ? " serie" : " series")
  let typoSeconde = (timeToRest === 1 ? " seconde" : " secondes")
  // Si repetition est une chaîne de caractère
  if (typeof repetition === "string") {
    return series + typoSerie + " x " + repetition + ", " + timeToRest + typoSeconde
  } else {
    return series + typoSerie + " x " + repetition + " reps, " + timeToRest + typoSeconde
  }
}
</script>

<template>
  <div>
    <h5 class="card-title ubuntu-regular fs-3">{{ props.data[0] }}</h5>
    <p class="card-text ubuntu-light-italic fs-5">{{ infoExercise(props.data[2], props.data[1], props.data[3]) }}</p>
  </div>
</template>