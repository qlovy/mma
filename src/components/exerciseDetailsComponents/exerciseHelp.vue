<script setup>
import {ref} from 'vue'
const help = ref(false)
const emit = defineEmits(['skipExercise'])
</script>

<template>
  <!--Bouton sous format texte pour demander de l'aide-->
  <button class="btn fixed-bottom ms-2 mb-2" type="button" @click="help = true">Besoin d'aide ?</button>
  <!--Menu d'aide-->
  <Transition>
    <div v-if="help" id="help-page" class="fixed-bottom mb-3">
      <div class="card card-body mx-3">
        <!--Bouton pour fermer la page-->
        <button aria-label="Close" class="btn-close" type="button" @click="help = false"></button>
        <p class="card-text ubuntu-regular fs-3 mx-auto">Que voulez-vous faire ?</p>
        <!--Action: passer l'exercice-->
        <button class="btn ubuntu-light fs-5" type="button" @click="$emit('skipExercise'); help = false;">Passer un exercice</button>
      </div>
    </div>
  </Transition>
</template>