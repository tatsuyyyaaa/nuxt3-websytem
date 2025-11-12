<template>
  <div>
    <h1>Categories</h1>
    <v-btn color="primary" @click="createCategoryDialog = true">
      add category</v-btn
    >
    <v-card>
      <v-data-table :items="categories" :headers="headers"> </v-data-table>
    </v-card>
    <v-dialog width="500" v-model="createCategoryDialog">
      <v-card>
        <v-toolbar color="primary">
          <v-toolbar-title> Create Category</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4"  v-model="createCategoryForm">
            <v-text-field v-model="categoryName">  </v-text-field>
            <v-btn @click="createCategories()" color="primary" block> Create </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script setup>
const categories = ref([]);
const headers = [
  {
    title: "Name",
    key: "name"},
   {
    title: "Date Created",
    key: "createdAt",
  },
];
const createCategoryDialog = ref(false);
const createCategoryForm = ref(null);
const categoryName = ref("");

const getCategories = async () => {
  console.log("Successfuly fetch to the categories!");
  const res = await $fetch("http://localhost:1337/api/categories");

  if (res) {
    console.log("Successfully fetch", res.data);
    categories.value = res.data;
  } else {
    console.log("Error!");
  }
};
const createCategories = async ()=>{
  
  console.log("Successfully Created", categoryName.value );
  let payload = {
    data:{
    name: categoryName.value
    }
  };
  const res = await $fetch("http://localhost:1337/api/categories" , {
    method: "POST",
    body: payload
  })
  if (res){
    console.log("Successfully Created", categoryName.value);
    getCategories();
  }else{
    console.log("error!")
  }

};
onMounted(() => {
  getCategories();
});
</script>   