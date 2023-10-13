<template>
  <planner :placeName="placeNameChanged"/>
</template>

<script>
import planner from '../components/Planning.vue'
import Map from '../components/Map.vue'

export default {
  name: "Plan",
  data() {
    return {
      placeName: '',
    };
  },
  components: {
    planner,
    Map
  },
};
</script>