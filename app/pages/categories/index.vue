<template>
  <div>
    <h3>Categories</h3>
  </div>

  <v-card>
    <v-data-table :items="categories" :headers="headers">
    </v-data-table>
  </v-card>
</template>

<script setup>
const categories = ref([])
const headers = [
    {
        title: 'Name', key: 'name',
        title: 'Date Created', key: 'createdAt'
    }
]
</script>   