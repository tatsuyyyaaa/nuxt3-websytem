<template>
  <v-app>
    <v-main class="bg-gradient">
      <slot />
    </v-main>
  </v-app>
</template>

<script setup>
</script>

<style scoped>
.bg-gradient {
  background: linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%);
  min-height: 100vh;
}
</style>