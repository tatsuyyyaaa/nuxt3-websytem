<template>
  <div>
    <h1>Users Page</h1>
    <v-btn color="primary" @click="createUserDialog = true">Add User</v-btn>

    <v-card class="mt-4">
      <v-data-table :items="users" :headers="headers" :loading="loading">
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn
            icon="mdi-eye"
            color="blue"
            size="small"
            @click="viewUser(item)"
          ></v-btn>
          <v-btn
            icon="mdi-pencil"
            color="green"
            size="small"
            @click="editUser(item)"
          ></v-btn>
          <v-btn
            icon="mdi-delete"
            color="red"
            size="small"
            @click="deleteUser(item)"
          ></v-btn>
        </template>
      </v-data-table>
    </v-card>

    <!-- CREATE DIALOG -->
    <v-dialog width="500" v-model="createUserDialog">
      <v-card>
        <v-toolbar color="green">
          <v-toolbar-title>Create User</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="createForm">
            <v-text-field v-model="userName" label="Full Name" required></v-text-field>
            <v-text-field v-model="userEmail" label="Email" type="email" required></v-text-field>
            <v-select v-model="userRole" :items="roles" label="Role" required></v-select>

            <v-btn @click="createUser()" color="green" block :loading="creating">Create</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- EDIT DIALOG -->
    <v-dialog width="500" v-model="editUserDialog">
      <v-card>
        <v-toolbar color="blue">
          <v-toolbar-title>Edit User</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="editForm">
            <v-text-field v-model="editUserData.name" label="Full Name" required></v-text-field>
            <v-text-field v-model="editUserData.email" label="Email" type="email" required></v-text-field>
            <v-select v-model="editUserData.role" :items="roles" label="Role" required></v-select>

            <v-btn @click="updateUser()" color="blue" block :loading="updating">Update</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- VIEW DIALOG -->
    <v-dialog width="500" v-model="viewUserDialog">
      <v-card>
        <v-toolbar color="info">
          <v-toolbar-title>User Details</v-toolbar-title>
        </v-toolbar>
        <v-card-text class="pt-5">
          <v-list>
            <v-list-item>
              <v-list-item-title>Name</v-list-item-title>
              <v-list-item-subtitle>{{ viewUserData.name }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Email</v-list-item-title>
              <v-list-item-subtitle>{{ viewUserData.email }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Role</v-list-item-title>
              <v-list-item-subtitle>{{ viewUserData.role }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Created At</v-list-item-title>
              <v-list-item-subtitle>{{ viewUserData.createdAt }}</v-list-item-subtitle>
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
const users = ref([]);
const loading = ref(false);
const creating = ref(false);
const updating = ref(false);

// Dialogs
const createUserDialog = ref(false);
const editUserDialog = ref(false);
const viewUserDialog = ref(false);

// Forms
const createForm = ref(null);
const editForm = ref(null);
const userName = ref("");
const userEmail = ref("");
const userRole = ref("user");
const editUserData = ref({});
const viewUserData = ref({});

// Snackbar
const snackbar = ref(false);
const snackbarColor = ref("");
const snackbarText = ref("");

const roles = ["admin", "staff", "user"];
const headers = [
  { title: "Name", key: "name" },
  { title: "Email", key: "email" },
  { title: "Role", key: "role" },
  { title: "Date Created", key: "createdAt" },
  { title: "Actions", key: "actions", sortable: false },
];

// GET ALL USERS
const getUsers = async () => {
  loading.value = true;
  try {
    const res = await $fetch("http://localhost:1337/api/users");
    if (res) {
      users.value = res.data;
    }
  } catch (err) {
    console.log("Error fetching users:", err);
    showMessage("Error loading users", "error");
  } finally {
    loading.value = false;
  }
};

// CREATE USER
const createUser = async () => {
  if (!createForm.value) return;
  
  const { valid } = await createForm.value.validate();
  if (!valid) return;
  
  creating.value = true;
  try {
    let payload = {
      data: {
        name: userName.value,
        email: userEmail.value,
        role: userRole.value,
      },
    };
    
    const res = await $fetch("http://localhost:1337/api/users", {
      method: "POST",
      body: payload,
    });

    showMessage("User created successfully!", "success");
    getUsers();
    createUserDialog.value = false;
    resetCreateForm();
    
  } catch (err) {
    console.log(err);
    showMessage("Error creating user", "error");
  } finally {
    creating.value = false;
  }
};

// VIEW USER
const viewUser = (user) => {
  viewUserData.value = { ...user };
  viewUserDialog.value = true;
};

// EDIT USER (open dialog)
const editUser = (user) => {
  editUserData.value = { ...user };
  editUserDialog.value = true;
};

// UPDATE USER
const updateUser = async () => {
  if (!editForm.value) return;
  
  const { valid } = await editForm.value.validate();
  if (!valid) return;
  
  updating.value = true;
  try {
    let payload = {
      data: {
        name: editUserData.value.name,
        email: editUserData.value.email,
        role: editUserData.value.role,
      },
    };
    
    const res = await $fetch(`http://localhost:1337/api/users/${editUserData.value.id}`, {
      method: "PUT",
      body: payload,
    });

    showMessage("User updated successfully!", "success");
    getUsers();
    editUserDialog.value = false;
    
  } catch (err) {
    console.log(err);
    showMessage("Error updating user", "error");
  } finally {
    updating.value = false;
  }
};

// DELETE USER
const deleteUser = async (user) => {
  if (!confirm(`Delete ${user.name}?`)) return;
  
  try {
    const res = await $fetch(
      `http://localhost:1337/api/users/${user.id}`,
      {
        method: "DELETE",
      }
    );

    showMessage("User deleted successfully!", "success");
    getUsers();
  } catch (err) {
    console.log(err);
    showMessage("Error deleting user", "error");
  }
};

// Reset forms
const resetCreateForm = () => {
  userName.value = "";
  userEmail.value = "";
  userRole.value = "user";
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
  getUsers();
});
</script>