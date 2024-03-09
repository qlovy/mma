<template>
    <img alt="Vue logo" src="./assets/logo.png" />
    <!--  <HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <Todo />
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
//import Todo from "@/components/Todo.vue";

export default {
    name: "App",
    components: {
        HelloWorld,
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    text-align: left;
    color: #2c3e50;
    margin-top: 20px;
}
</style>
