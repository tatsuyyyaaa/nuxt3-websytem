<template>
  <v-app>
 
    <v-navigation-drawer v-model="drawer" color="blue">
      <v-list>
        <v-list-item prepend-avatar="https://cdn.kami.com.ph/images/1120/0fgjhs39o5t36qk2eg.jpeg?v=1" 
          subtitle="mmanansala266@gmail.com" title="Mark Manansala">
        </v-list-item>
      </v-list>

      <v-divider></v-divider>

      
      <v-list density="compact" nav>
        
        <v-list-item prepend-icon="mdi-view-dashboard" title="Dashboard" value="dashboard" to="/dashboard"></v-list-item>
        
        
        <v-list-item prepend-icon="mdi-package-variant" title="Items" value="items" to="/items"></v-list-item>
        
        
        <v-list-item prepend-icon="mdi-account-group" title="Users" value="users" to="/users"></v-list-item>
        
        
        <v-list-item prepend-icon="mdi-cash-multiple" title="Transactions" value="transactions" to="/transactions"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    
    <v-app-bar :elevation="2">
      <template v-slot:prepend>
        <v-app-bar-nav-icon variant="text" @click="drawer = !drawer"></v-app-bar-nav-icon>
      </template>

      <v-app-bar-title>Web Management System</v-app-bar-title>
      
      
      <template v-slot:append>
        <v-btn icon="mdi-logout" variant="text" @click="logout" title="Logout"></v-btn>
      </template>
    </v-app-bar>

    
    <v-main>
      <v-container fluid>
        <slot />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
const drawer = ref(true)

const logout = () => {
  // Redirect to login
  window.location.href = '/login'
}
</script>

<style>
</style>