<template>
  <div id="app">
    <ticket-list msg="Liste des ticket"/>
  </div>
</template>

<script>
import TicketList from './components/TicketList.vue';

export default {
  name: 'App',
  components: {
    TicketList
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
}
</style>
