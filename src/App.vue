<script setup>
import { ref } from 'vue';

const color = ref('dark');
function toggleColor(){
  color.value = color.value === 'dark' ? 'white' : 'dark';
}
</script>

<template id="app">
  <!--Ubuntu Police-->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,300;0,400;0,500;1,300;1,400;1,500&display=swap" rel="stylesheet">

  <div id="body">
    <h1 class="ubuntu-medium title">MMA</h1>
  </div>

  <div class="card boxes" id="box-1">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular">Force Poussée</h5>
      <p class="card-text ubuntu-light-italic ">Lundi et Vendredi</p>
    </div>
  </div>

  <div class="card boxes">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular">Force Tirage</h5>
      <p class="card-text ubuntu-light-italic ">Mercredi et Samedi</p>
    </div>
  </div>

  <div class="card boxes">
    <div class="card-body">
      <h5 class="card-title ubuntu-regular">Jambes</h5>
      <p class="car ubuntu-light-italic d-text">Jeudi</p>
    </div>
  </div>


</template>

<script>
export default {
  name: "App",
};
</script>

<style>
/* For the color: https://paletton.com/#uid=13i0u0ksnFdhuNAn1IHCVyOCHoW */
#body {
  position: absolute;
  background-color: #15baba;
  width: 393px;
  height: 852px;
  top: 0;
  left: 0;
}

.title {
  padding-top: 20px;
  text-align: center;
  font-size: 40px;
}
/*Les boîtes*/
.boxes {
  margin: 50px 20px 0 20px;
  background-color: #00a2a2;
}

#box-1 {
  margin-top: 150px;
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

/*Bootstrap import*/
@import '~bootstrap/dist/css/bootstrap.css';

</style>
