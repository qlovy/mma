<script setup>
const props = defineProps({
  ctx: Object
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
        props.ctx.actualUseRef.nom
      }}{{ props.ctx.exercisesBook[ctx.type].alterne && !props.ctx.warmup? " en alternance" : "" }}</h5>
    <p class="card-text ubuntu-light-italic fs-5">
      {{ infoExercise(props.ctx.actualUseRef.repetitions, props.ctx.actualUseRef.series, props.ctx.actualUseRef.recuperation) }}</p>
  </div>
</template>