<template>
    <LaunchList />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import LaunchList from './components/LaunchList.vue';

export default defineComponent({
  name: 'App',
  components: {
    LaunchList,
  },
});
</script>

<style>
</style>
