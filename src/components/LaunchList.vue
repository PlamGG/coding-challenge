<template>
  <div class="container mx-auto">
    <h1 class="text-3xl font-bold text-center my-6">Launches</h1>

    <div v-if="launches.length > 0">
      <table class="min-w-full bg-white">
        <thead>
          <tr class="w-full bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
            <th @click="sort('name')" class="py-3 px-6 text-left cursor-pointer">
              Name
              <span v-if="sortColumn === 'name'">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
            </th>
            <th @click="sort('date_utc')" class="py-3 px-6 text-left cursor-pointer">
              Launch Date
              <span v-if="sortColumn === 'date_utc'">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
            </th>
            <th class="py-3 px-6 text-left">Image</th>
            <th class="py-3 px-6 text-left">Crew</th>
            <th class="py-3 px-6 text-left">Status</th>
          </tr>
        </thead>
        <tbody class="text-gray-600 text-sm font-light">
          <tr
            v-for="(launch, index) in sortedLaunches"
            :key="`${launch.id}-${index}`"
            @click="openModal(launch)"
            class="border-b border-gray-200 hover:bg-gray-100 cursor-pointer"
          >
            <td class="py-3 px-6 text-left whitespace-nowrap">{{ launch.name }}</td>
            <td class="py-3 px-6 text-left">{{ new Date(launch.date_utc).toLocaleString() }}</td>
            <td class="py-3 px-6 text-left">
              <img :src="launch.links.patch.small" alt="Mission Patch" class="w-12 h-12" />
            </td>
            <td class="py-3 px-6 text-left">{{ launch.crew.length }}</td>
            <td class="py-3 px-6 text-left">{{ getStatus(launch) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <p v-else>Loading...</p>

    <LaunchDetails v-if="selectedLaunch" :launch="selectedLaunch" @close="closeModal" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, computed } from 'vue';
import LaunchDetails from './LaunchDetails.vue';

interface Launch {
  id: string;
  name: string;
  date_utc: string;
  links: {
    patch: {
      small: string;
    };
  };
  crew: any[];
}

export default defineComponent({
  name: 'LaunchList',
  components: {
    LaunchDetails,
  },
  setup() {
    const launches = ref<Launch[]>([]);
    const selectedLaunch = ref<Launch | null>(null);
    const sortColumn = ref<string>('date_utc');
    const sortOrder = ref<'asc' | 'desc'>('desc');

    const fetchLaunches = async () => {
      try {
        const allResponse = await fetch('https://api.spacexdata.com/v5/launches');
        const allLaunches = await allResponse.json();

        const pastResponse = await fetch('https://api.spacexdata.com/v4/launches/past');
        const pastLaunches = await pastResponse.json();

        const upcomingResponse = await fetch('https://api.spacexdata.com/v4/launches/upcoming');
        const upcomingLaunches = await upcomingResponse.json();

        launches.value = [...allLaunches, ...pastLaunches, ...upcomingLaunches];
      } catch (error) {
        console.error('Failed to fetch launches:', error);
      }
    };

    const getStatus = (launch: Launch) => {
      const now = new Date();
      const launchDate = new Date(launch.date_utc);
      return launchDate < now ? 'Past' : 'Upcoming';
    };

    const openModal = (launch: Launch) => {
      selectedLaunch.value = launch;
    };

    const closeModal = () => {
      selectedLaunch.value = null;
    };

    const sort = (column: string) => {
      if (sortColumn.value === column) {
        sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
      } else {
        sortColumn.value = column;
        sortOrder.value = 'asc';
      }
    };

    const sortedLaunches = computed(() => {
      return [...launches.value].sort((a, b) => {
        let comparison = 0;
        if (sortColumn.value === 'name') {
          comparison = a.name.localeCompare(b.name);
        } else if (sortColumn.value === 'date_utc') {
          comparison = new Date(a.date_utc).getTime() - new Date(b.date_utc).getTime();
        }
        return sortOrder.value === 'asc' ? comparison : -comparison;
      });
    });

    onMounted(() => {
      fetchLaunches();
    });

    return {
      launches,
      selectedLaunch,
      openModal,
      closeModal,
      getStatus,
      sort,
      sortColumn,
      sortOrder,
      sortedLaunches,
    };
  },
});
</script>

<style>
</style>