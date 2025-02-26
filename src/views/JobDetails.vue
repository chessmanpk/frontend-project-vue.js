<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import jobData from '@/jobs.json';

const route = useRoute();
const job = ref(null);

onMounted(() => {
  const jobId = route.params.id;
  job.value = jobData.jobs.find(j => j.id === jobId);
});
</script>

<template>
  <div v-if="job">
    <h1>{{ job.title }}</h1>
    <div class="job-details">
      <p>{{ job.description }}</p>
      <div class="job-info">
        <p><strong>Type:</strong> {{ job.type }}</p>
        <p><strong>Location:</strong> {{ job.location }}</p>
        <p><strong>Salary:</strong> {{ job.salary }}</p>
      </div>
      <div class="company-info">
        <h2>{{ job.company.name }}</h2>
        <p>{{ job.company.description }}</p>
        <p><strong>Contact Email:</strong> {{ job.company.contactEmail }}</p>
        <p><strong>Contact Phone:</strong> {{ job.company.contactPhone }}</p>
      </div>
    </div>
  </div>
  <div v-else>
    Job not found
  </div>
</template>