<template>
  <div>
    <h3>Categories</h3>

  <v-card>
    <v-data-table :items="categories" :headers="headers">
    </v-data-table>
  </v-card>
  </div>
</template>

<script setup>
const categories = ref([])
const headers = [
    {
        title: 'Name', key: 'name',
        title: 'Date Created', key: 'createdAt'
    }
]
const getCategories = async () => {
    console.log("Successfuly fetch to the categories!")
    const res = await $fetch('http://localhost:1337/api/categories');

    if(res) {
        console.log("Successfully fetch", res.data)
        categories.value = res.data
    } else {
        console.log("Error!");
    }

}
onMounted(()=> {
    getCategories();
})

</script>   