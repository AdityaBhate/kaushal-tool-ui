<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light shadow">
    <div class="container">
      <a class="navbar-brand" href="#">Tool</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
    </div>
  </nav>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <div class="card my-5 p-4 shadow border-0 rounded">
          <form @submit.prevent="onSubmitSearch">
            <div class="mb-3">
              <label for="siteSearch" class="form-label">Site to search</label>
              <input type="text" class="form-control" id="siteSearch" placeholder="Site to search" v-model="search" required>
            </div>
            <div class="mb-3">
              <label for="searchStartSheet" class="form-label">Search Start Sheet</label>
              <select class="form-select" aria-label="Default select example" id="searchStartSheet" v-model="start" required>
                <option :value="index" v-for="(item, index) in list" :key="index">{{item}}</option>
              </select>
            </div>
            <div class="mb-3">
              <button type="submit" class="btn btn-primary" :disabled="loading">
                {{ loading ? 'Searching...' : 'Search' }}
              </button>
            </div>
            <div class="alert alert-danger mb-3" role="alert" v-if="error.length">
              {{ error }}
            </div>
            <div v-if="result.length">
              <p v-for="(item, index) in result" :key="index">
                {{ item.sheet }} -
                <span v-if="item.present" class="text-success">Present</span>
                <span v-else class="text-danger">Not Present</span>
              </p>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';

const list = ref([]);
const result = ref([]);
const search = ref('');
const start = ref(0);
const error = ref('');
const loading = ref(false);

onMounted(async () => {
  const res = await axios.get(`${import.meta.env.VITE_API_BASE_URL}sheet-list`);
  list.value = res.data.list
})

async function onSubmitSearch() {
  try {
    loading.value = true;
    error.value = '';
    result.value = []
    const res = await axios.post(`${import.meta.env.VITE_API_BASE_URL}search`, {
      search: search.value,
      start: start.value
    })
    result.value = res.data.result
  } catch (err) {
    if (err.response && err.response.data.message) {
      error.value = err.response.data.message;
    } else {
      error.value = err.message;
    }
  } finally {
    loading.value = false;
  }
}
</script>
