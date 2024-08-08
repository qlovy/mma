<script setup>
const props = defineProps({
  ctx: Object
})

// Définis les informations à afficher en dessous du nom de l'exercice
function infoExercise(repetition, series, timeToRest) {
  // Change l'orthorgraphe en fonction du nombre a affiché
  let typo_serie = (series === 1 ? " serie" : " series")
  let typo_seconde = (timeToRest === 1 ? " seconde" : " secondes")
  // Si repetition est une chaîne de caractère
  if (typeof repetition === "string") {
    return repetition + " x " + series + typo_serie + ", " + timeToRest + typo_seconde
  } else {
    return repetition + " reps x " + series + typo_serie + ", " + timeToRest + typo_seconde
  }
}
</script>

<template>
  <div>
    <h5 class="card-title ubuntu-regular fs-3">{{
        props.ctx.actualUseRef.nom
      }}{{ props.ctx.exercisesBook[props.ctx.type].alterne && !props.ctx.warmup ? " en alternance" : "" }}</h5>
    <p class="card-text ubuntu-light-italic fs-5">
      {{
        infoExercise(props.ctx.actualUseRef.repetitions, props.ctx.actualUseRef.series, props.ctx.actualUseRef.recuperation)
      }}</p>
  </div>
</template>