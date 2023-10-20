<template>
  <WorkScheduleForm></WorkScheduleForm>
</template>

<script>
import WorkScheduleForm from './components/WorkScheduleForm.vue';

export default {
  name: 'App',
  components: {
    WorkScheduleForm
  }
}
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-left: 2%;
	margin-top: 1%;
}
</style>
