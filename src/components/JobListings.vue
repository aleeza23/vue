<script setup>
import { reactive, ref } from 'vue';  // same as useState in react
import JobListing from './JobListing.vue';
import { defineProps, onMounted } from 'vue';
import { RouterLink } from 'vue-router';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';
import axios from 'axios';


defineProps({
    limit: Number,
    showBtn: {
        type: Boolean,
        default: false
    }
});
const state = reactive({
    jobs: [],
    isLoading: true
})
// console.log(jobs.value)

onMounted((async () => {
    try {
        const res = await axios.get("https://vueserver-production.up.railway.app/jobs");
        state.jobs = res.data
    } catch (error) {
        console.error("Error fetching jobs.", error)
    } finally {
        state.isLoading = false
    }
}))
</script>

<template>
    <section class="bg-green-50 px-4 py-10">
        <div class="container-xl lg:container m-auto">
            <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
                Browse Jobs
            </h2>

            <!-- spinner -->
            <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
                <PulseLoader />
            </div>
            <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <JobListing v-for="job in state.jobs.slice(0, limit || state.jobs.length)" :key="job.id" :job="job" />
            </div>
        </div>

        <section v-if="showBtn" class="m-auto max-w-lg my-10 px-6">
            <RouterLink to="/jobs"
                class="block bg-black text-white text-center py-4 px-6 rounded-xl cursor-pointer hover:bg-gray-700">
                View All Jobs </RouterLink>
        </section>
    </section>
</template>
