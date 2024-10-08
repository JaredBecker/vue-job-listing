<script setup>
import { reactive, defineProps, onMounted } from "vue";
import JobListing from "./JobListing.vue";
import axios from "axios";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
});

// Reactive only takes objects. It does not take primitives likes strings or numbers or booleans.
// ref() has a .value prop for reassigning, reactive() doesn't have that and can't be reassigned. You can change props in the object but not the object itself.
const state = reactive({
  jobs: [],
  isLoading: true,
});

onMounted(async () => {
  try {
    // api is proxied to http://localhost:5000 in vite.config.js
    const response = await axios.get("/api/jobs");
    state.jobs = response.data;
  } catch (error) {
    console.error("Error fetching jobs:", error);
  } finally {
    setTimeout(() => {
      state.isLoading = false;
    }, 1500);
  }
});
</script>

<template>
  <section class="bg-green-50 px-4 py-10">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
        Browse Jobs
      </h2>
      <!-- Show loading spinner while loading is true -->
      <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
        <PulseLoader />
      </div>
      <!-- Show job listing when done loading -->
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in state.jobs.slice(0, limit) || state.jobs"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>

  <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
    <RouterLink
      to="/jobs"
      class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
      >View All Jobs
    </RouterLink>
  </section>
</template>
