<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center">
    <div class="bg-white p-6 rounded-lg w-2/3">
      <button @click="$emit('close')" class="text-red-500 font-bold float-right">X</button>
      <h2 class="text-2xl font-bold">{{ launch.name }}</h2>
      <p><strong>Details:</strong> {{ launch.details || 'No details available' }}</p>
      <p><strong>Rocket:</strong> {{ launch.rocket }}</p>
      <p><strong>Launchpad:</strong> {{ launch.launchpad }}</p>

      <p><strong>Crew:</strong> {{ launch.crew || 'No details available' }}</p>

      
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';

export default defineComponent({
  name: 'LaunchDetails',
  props: {
    launch: {
      type: Object as PropType<any>,
      required: true,
    },
  },
});
</script>

<style>
</style>
