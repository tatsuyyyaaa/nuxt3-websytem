<template>
  <v-app>
    <v-navigation-drawer v-model= "drawer" >
    
      <v-list>
        <v-list-item prepend-avatar="https://randomuser.me/api/portraits/women/85.jpg" subtitle="sandra_a88@gmailcom"
          title="Sandra Adams"></v-list-item>
      </v-list>

      <v-divider></v-divider>

      <v-list density="compact" nav>
        <v-list-item prepend-icon="mdi-view-dashboard" title="Dashboard" value="dashboard" to = "/"></v-list-item>
        <v-list-item prepend-icon="mdi-invoice-list" title="Inventory" value="items" to = "/" inventory ></v-list-item>
        <v-list-item prepend-icon="mdi-shape" title="Categories" value="category" to = "/"></v-list-item>
        <v-list-item prepend-icon="mdi-cart-arrow-down" title="Suppliers" value="supply" to = "/"></v-list-item>

      </v-list>
    </v-navigation-drawer>
    <v-app-bar :elevation="2">
      <template v-slot:prepend>
        <v-app-bar-nav-icon variant ="text" @click =  "drawer =!drawer"></v-app-bar-nav-icon>
      </template>

      <v-app-bar-title>Application Bar</v-app-bar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <slot />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
const drawer = ref (true)
</script>

<style></style>