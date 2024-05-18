<script setup>
const props = defineProps({
  actualUseRef: Object,
  alternating: Boolean,
})

// Définis les informations à afficher en dessous du nom de l'exercice
function infoExercise(repetition, series, timeToRest) {
  let s = (series === 1 ? " serie" : " series") // Change l'orthorgraphe en fonction du nombre affiché
  // Si repetition est une chaîne de caractère
  if (typeof repetition === "string") {
    return repetition + " x " + series + s + ", " + timeToRest + " secondes"
  } else {
    return repetition + " reps x " + series + s + ", " + timeToRest + " secondes"
  }
}
</script>

<template>
  <div>
    <h5 class="card-title ubuntu-regular fs-3">{{
        props.actualUseRef.nom
      }}{{ props.alternating? " en alternance" : "" }}</h5>
    <p class="card-text ubuntu-light-italic fs-5">
      {{ infoExercise(props.actualUseRef.repetitions, props.actualUseRef.series, props.actualUseRef.recuperation) }}</p>
  </div>
</template>

<script>
export default {
  name: 'exerciseInformations',
  props: {
    actualUseRef: Object,
    alternating: Boolean,
  }
}
</script>