<script setup>
import BackButton from '@/components/BackButton.vue';
import { ref, onMounted } from 'vue';
import { useRoute, RouterLink, useRouter } from 'vue-router';
import { useToast } from 'vue-toastification';

import axios from 'axios';

// Import the PulseLoader component if you have it available in your project
// If it's from a package like 'vue-spinner', you would import it like this:
// import { PulseLoader } from 'vue-spinner';

const route = useRoute();
const router = useRouter();
const toast = useToast();

const jobId = route.params.id;

// Convert reactive state to individual refs
const job = ref({});
const isLoading = ref(true);

const deleteJob = async () => {
  try {
    const confirm = window.confirm('Are you sure you want to delete this job?');
    if (confirm) {
      await axios.delete(`/api/jobs/${jobId}`);
      toast.success('Job Deleted Successfully!');
      router.push('/jobs');
    }
  } catch (error) {
    console.error('Error deleting the job', error);
    toast.error('Error Deleting the Job');
  }
}

onMounted(async() => {
    try {
        const response = await axios.get(`http://localhost:5000/jobs/${jobId}`);
        job.value = response.data;
        isLoading.value = false; // Set loading to false after data is fetched
    } catch (error) {
        console.error('Error Fetching Job!', error);
        isLoading.value = false; // Also set loading to false on error
    }
});
</script>

<template>
  <BackButton />
    <section v-if="!isLoading" class="bg-green-50">
      <div class="container m-auto py-10 px-6">
        <!-- Changed grid class to use standard Tailwind grid -->
        <div class="grid grid-cols-1 md:grid-cols-3 w-full gap-6">
          <main class="md:col-span-2">
            <div
              class="bg-white p-6 rounded-lg shadow-md text-center md:text-left"
            >
              <div class="text-gray-500 mb-4">{{ job.type }}</div>
              <h1 class="text-3xl font-bold mb-4">{{ job.title }}</h1>
              <div
                class="text-gray-500 mb-4 flex align-middle justify-center md:justify-start"
              >
                <i
                  class="pi pi-map-marker text-xl text-orange-700 mr-2"
                ></i>
                <p class="text-orange-700">{{ job.location }}</p>
              </div>
            </div>

            <div class="bg-white p-6 rounded-lg shadow-md mt-6">
              <h3 class="text-green-800 text-lg font-bold mb-6">
                Job Description
              </h3>

              <p class="mb-4">
                {{ job.description }}
              </p>

              <h3 class="text-green-800 text-lg font-bold mb-2">Salary</h3>

              <p class="mb-4">{{ job.salary }} / Year</p>
            </div>
          </main>

          <!-- Sidebar -->
          <aside class="md:col-span-1">
            <!-- Company Info -->
            <div class="bg-white p-6 rounded-lg shadow-md">
              <h3 class="text-xl font-bold mb-6">Company Info</h3>

              <h2 class="text-2xl">{{ job.company?.name }}</h2>

              <p class="my-2">
                {{ job.company?.description }}
              </p>

              <hr class="my-4" />

              <h3 class="text-xl">Contact Email:</h3>

              <p class="my-2 bg-green-100 p-2 font-bold">
                {{ job.company?.contactEmail }}
              </p>

              <h3 class="text-xl">Contact Phone:</h3>

              <p class="my-2 bg-green-100 p-2 font-bold">{{ job.company?.contactPhone }}</p>
            </div>

            <!-- Manage -->
            <div class="bg-white p-6 rounded-lg shadow-md mt-6">
              <h3 class="text-xl font-bold mb-6">Manage Job</h3>
              <RouterLink
                :to="`/jobs/edit/${job.id}`"
                class="bg-green-500 hover:bg-green-600 
                text-white text-center 
                font-bold py-2 px-4 
                rounded-full w-full 
                focus:outline-none 
                focus:shadow-outline 
                mt-4 block"
                >Edit Job
              </RouterLink>

              <button @click="deleteJob"
                class="bg-red-500 hover:bg-red-600 text-white 
                font-bold py-2 px-4 rounded-full w-full 
                focus:outline-none 
                focus:shadow-outline mt-4 block"
              >
                Delete Job
              </button>
            </div>
          </aside>
        </div>
      </div>
    </section>

    <!-- Replace PulseLoader with a simple loading indicator -->
    <div v-else class="text-center text-gray-500 py-20">
      <div class="flex justify-center items-center">
        <div class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-green-500"></div>
      </div>
      <p class="mt-4 text-lg">Loading job details...</p>
    </div>
</template>