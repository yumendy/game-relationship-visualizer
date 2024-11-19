<template>
  <div id="app">
    <PlayerRolesVisualization :data="jsonData" v-if="isDataLoaded" />
  </div>
</template>

<script>
import PlayerRolesVisualization from './components/PlayerRolesVisualization.vue';

export default {
  name: 'App',
  components: {
    PlayerRolesVisualization
  },
  data() {
    return {
      jsonData: null,
      isDataLoaded: false
    };
  },
  async created() {
    try {
      const response = await fetch('/data.json');
      this.jsonData = await response.json();
      this.isDataLoaded = true;
    } catch (error) {
      console.error('Error loading JSON data:', error);
    }
  }
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  //height: 100vh;
}
</style>