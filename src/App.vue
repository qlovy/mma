<script setup>
import { ref } from 'vue';

const color = ref('dark');
function toggleColor(){
  color.value = color.value === 'dark' ? 'white' : 'dark';
}
</script>

<template id="app">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap" rel="stylesheet">
  <div id="body">
    <h1 class="ubuntu-medium title">MMA</h1>
  </div>
  <div class="boxes ubuntu-light" id="box-1">
    <p>Some text</p>
  </div>
  <div class="boxes ubuntu-light" id="box-2">
    <p>Some text</p>
  </div>
  <div class="boxes ubuntu-light" id="box-3">
    <p>Some text</p>
  </div>
  <div class="boxes ubuntu-light" id="box-4">
    <p>Some text</p>
  </div>

</template>

<script>
export default {
  name: "App",
};
</script>

<style>
// For the color: https://paletton.com/#uid=13i0u0ksnFdhuNAn1IHCVyOCHoW
#body {
  position: absolute;
  background-color: #15baba;
  width: 393px;
  height: 852px;
  top: 0;
  left: 0;
}

.title {
  text-align: center;
}
/*Les boîtes*/
.boxes {
  display: block;
  background-color: #00a2a2;
  border-radius: 5px;
  text-align: center;
  padding: 30px;
  top: 100px;
}

#box-1 {
  position: absolute;
  left: 40px;
}

#box-2 {
  position: absolute;
  right: 40px;
}

#box-3 {
  position: absolute;
  left: 40px;
  top: 260px;
}

#box-4 {
  position: absolute;
  top: 260px;
  right: 40px;
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


</style>
