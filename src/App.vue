<script setup>
import TaskProgress from './components/taskProgress/TaskProgress.vue'
import EventList from './components/eventList/index.vue'
</script>

<template>
  <!-- <div id="app"> -->
    <!-- <TaskProgress /> -->
    <EventList />
  <!-- </div> -->
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  /* width: 100%; */
  /* min-height: 100vh; */
  background: #000;
}
</style>