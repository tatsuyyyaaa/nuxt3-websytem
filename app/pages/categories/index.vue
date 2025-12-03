<template>
  <div>
    <h1>Categories Page {{ categoryName }}</h1>
    <v-btn color="primary" @click="createCategoryDialog = true"
      >Add Category</v-btn
    >

    <v-card>
      <v-data-table :items="categories" :headers="headers">
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn
            icon="mdi-delete"
            color="red"
            size="small"
            @click="deleteCategories(item)"
          ></v-btn>
        </template>
      </v-data-table>
    </v-card>

    <v-dialog width="500" v-model="createCategoryDialog">
      <v-card>
        <v-toolbar color="green">
          <v-toolbar-title>Create Category</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" v-model="createCategoryForm">
            <v-text-field v-model="categoryName"></v-text-field>

            <v-btn @click="createCategories()" color="green" block
              >Create</v-btn
            >
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}</v-snackbar
    >
  </div>
</template>

<script  setup>
const categories = ref([]);
const headers = [
  { title: "Name", key: "name" },
  { title: "Date Created", key: "createdAt" },
  { title: "", key: "actions" },
];
const createCategoryDialog = ref(false);
const createCategoryForm = ref(null);
const categoryName = ref("");
const snackbar = ref(false);
const snackbarColor = ref("");
const snackbarText = ref("Hello World");

const getCategories = async () => {
  console.log("Successfully fetch the categories!");
  const res = await $fetch("http://localhost:1337/api/categories");

  if (res) {
    console.log("Successfully fetched", res.data);
    categories.value = res.data;
  } else {
    console.log("Error!");
  }
};

const deleteCategories = async (item) => {
  console.log(item.documentId);
  try {
    const res = await $fetch(
      `http://localhost:1337/api/categories/${item.documentId}`,
      {
        method: "DELETE",
      }
    );

    console.log("Successfully Deleted!");
    snackbarColor.value = "green";
    snackbarText.value = "Successfully Deleted!";
    snackbar.value = true;
    getCategories();
  } catch (err) {
    console.log(err);
  }
};

const createCategories = async () => {
  try {
    let payload = {
      data: {
        name: categoryName.value,
      },
    };
    const res = await $fetch("http://localhost:1337/api/categories", {
      method: "POST",
      body: payload,
    });

    if (res) {
      console.log("Successfully Created", categoryName.value);
      snackbarColor.value = "green";
      snackbarText.value = "Successfully Created!";
      snackbar.value = true;
      getCategories();
    }
  } catch (err) {
    console.log(err.data.error.message);
    if (err) {
      snackbarColor.value = "error";
      snackbarText.value = "Category name already exist!";
      snackbar.value = true;
    }
  }
};

onMounted(() => {
  getCategories();
});
</script>

<style>
</style>