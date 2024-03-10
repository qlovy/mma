<script setup>
import { ref } from "vue";
const title = ref("MMA");
</script>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<template>
    <h1>{{ title }}</h1>
</template>
