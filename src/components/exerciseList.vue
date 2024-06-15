<script setup>
const props = defineProps({
  array: Array,
})
</script>

<template>
  <div class="blue-theme">
    <!--Le titre-->
    <h1 class="ubuntu-medium title mt-4 mb-5">MMA</h1>
    <!--Les différents entrainements-->
    <div class="card boxes blue-theme-boxes" v-for="(item, index) in props.array" @click="$emit('func', index)">
      <div class="card-body">
        <h5 class="card-title ubuntu-regular fs-2">{{ item.nom }}</h5>
        <p class="card-text ubuntu-light-italic fs-5">{{ item.jour }}</p>
      </div>
    </div>
  </div>
</template>
<
<style>
.title {
  padding-top: 30px;
  text-align: center;
  font-size: 50px;
}

.boxes {
  margin: 0 30px 70px 30px;
}

.blue-theme {
  background-color: #15baba;
}

.blue-theme-boxes {
  background-color: #00a2a2;
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

/*Bootstrap import*/
@import '~bootstrap/dist/css/bootstrap.css';
</style>