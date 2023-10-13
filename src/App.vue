<template>
  <div id="app">
  <NavBar />

  </div>
  <RouterView />
  
</template>

<script>
import { RouterView } from "vue-router";
import Planner from './components/Planning.vue'
import NavBar from './views/Navbar.vue'

export default {
  name: "App",
  components: {
    RouterView,
    Planner,
    NavBar,
},
};
</script>