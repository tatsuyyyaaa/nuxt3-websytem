<template>
  <div>
    <h1>Items Page</h1>
    <v-btn color="primary" @click="createItemDialog = true">Add Item</v-btn>

    <v-card class="mt-4">
      <v-data-table :items="items" :headers="headers" :loading="loading">
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn
            icon="mdi-eye"
            color="blue"
            size="small"
            @click="viewItem(item)"
          ></v-btn>
          <v-btn
            icon="mdi-pencil"
            color="green"
            size="small"
            @click="editItem(item)"
          ></v-btn>
          <v-btn
            icon="mdi-delete"
            color="red"
            size="small"
            @click="deleteItem(item)"
          ></v-btn>
        </template>
      </v-data-table>
    </v-card>

    <!-- CREATE DIALOG -->
    <v-dialog width="500" v-model="createItemDialog">
      <v-card>
        <v-toolbar color="green">
          <v-toolbar-title>Create Item</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="createForm">
            <v-text-field v-model="itemName" label="Item Name" required></v-text-field>
            <v-text-field v-model="itemPrice" label="Price" type="number" required></v-text-field>
            <v-text-field v-model="itemQuantity" label="Quantity" type="number" required></v-text-field>

            <v-btn @click="createItem()" color="green" block :loading="creating">Create</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

  
    <v-dialog width="500" v-model="editItemDialog">
      <v-card>
        <v-toolbar color="blue">
          <v-toolbar-title>Edit Item</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="editForm">
            <v-text-field v-model="editItemData.name" label="Item Name" required></v-text-field>
            <v-text-field v-model="editItemData.price" label="Price" type="number" required></v-text-field>
            <v-text-field v-model="editItemData.quantity" label="Quantity" type="number" required></v-text-field>

            <v-btn @click="updateItem()" color="blue" block :loading="updating">Update</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    
    <v-dialog width="500" v-model="viewItemDialog">
      <v-card>
        <v-toolbar color="info">
          <v-toolbar-title>Item Details</v-toolbar-title>
        </v-toolbar>
        <v-card-text class="pt-5">
          <v-list>
            <v-list-item>
              <v-list-item-title>Name</v-list-item-title>
              <v-list-item-subtitle>{{ viewItemData.name }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Price</v-list-item-title>
              <v-list-item-subtitle>${{ viewItemData.price }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Quantity</v-list-item-title>
              <v-list-item-subtitle>{{ viewItemData.quantity }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Created At</v-list-item-title>
              <v-list-item-subtitle>{{ viewItemData.createdAt }}</v-list-item-subtitle>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<script setup>
const items = ref([]);
const loading = ref(false);
const creating = ref(false);
const updating = ref(false);


const createItemDialog = ref(false);
const editItemDialog = ref(false);
const viewItemDialog = ref(false);


const createForm = ref(null);
const editForm = ref(null);
const itemName = ref("");
const itemPrice = ref("");
const itemQuantity = ref("");
const editItemData = ref({});
const viewItemData = ref({});


const snackbar = ref(false);
const snackbarColor = ref("");
const snackbarText = ref("");

const headers = [
  { title: "Name", key: "name" },
  { title: "Price", key: "price" },
  { title: "Quantity", key: "quantity" },
  { title: "Date Created", key: "createdAt" },
  { title: "Actions", key: "actions", sortable: false },
];


const getItems = async () => {
  loading.value = true;
  try {
    const res = await $fetch("http://localhost:1337/api/items");
    if (res) {
      items.value = res.data;
    }
  } catch (err) {
    console.log("Error fetching items:", err);
    showMessage("Error loading items", "error");
  } finally {
    loading.value = false;
  }
};


const createItem = async () => {
  if (!createForm.value) return;
  
  const { valid } = await createForm.value.validate();
  if (!valid) return;
  
  creating.value = true;
  try {
    let payload = {
      data: {
        name: itemName.value,
        price: itemPrice.value,
        quantity: itemQuantity.value,
      },
    };
    
    const res = await $fetch("http://localhost:1337/api/items", {
      method: "POST",
      body: payload,
    });

    showMessage("Item created successfully!", "success");
    getItems();
    createItemDialog.value = false;
    resetCreateForm();
    
  } catch (err) {
    console.log(err);
    showMessage("Error creating item", "error");
  } finally {
    creating.value = false;
  }
};


const viewItem = (item) => {
  viewItemData.value = { ...item };
  viewItemDialog.value = true;
};


const editItem = (item) => {
  editItemData.value = { ...item };
  editItemDialog.value = true;
};


const updateItem = async () => {
  if (!editForm.value) return;
  
  const { valid } = await editForm.value.validate();
  if (!valid) return;
  
  updating.value = true;
  try {
    let payload = {
      data: {
        name: editItemData.value.name,
        price: editItemData.value.price,
        quantity: editItemData.value.quantity,
      },
    };
    
    const res = await $fetch(`http://localhost:1337/api/items/${editItemData.value.id}`, {
      method: "PUT",
      body: payload,
    });

    showMessage("Item updated successfully!", "success");
    getItems();
    editItemDialog.value = false;
    
  } catch (err) {
    console.log(err);
    showMessage("Error updating item", "error");
  } finally {
    updating.value = false;
  }
};


const deleteItem = async (item) => {
  if (!confirm(`Delete ${item.name}?`)) return;
  
  try {
    const res = await $fetch(
      `http://localhost:1337/api/items/${item.id}`,
      {
        method: "DELETE",
      }
    );

    showMessage("Item deleted successfully!", "success");
    getItems();
  } catch (err) {
    console.log(err);
    showMessage("Error deleting item", "error");
  }
};

// Reset forms
const resetCreateForm = () => {
  itemName.value = "";
  itemPrice.value = "";
  itemQuantity.value = "";
  if (createForm.value) {
    createForm.value.reset();
  }
};

const showMessage = (message, color = "success") => {
  snackbarText.value = message;
  snackbarColor.value = color;
  snackbar.value = true;
};

onMounted(() => {
  getItems();
});
</script>