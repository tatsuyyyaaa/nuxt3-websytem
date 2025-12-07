<template>
  <div class="fill-height d-flex align-center justify-center">
    <v-card width="400" class="pa-6">
      <v-card-title class="text-center">
        <h2>Login</h2>
      </v-card-title>
      <v-card-text>
        <v-text-field label="Email" class="mb-3"></v-text-field>
        <v-text-field label="Password" type="password" class="mb-4"></v-text-field>
        <v-btn color="primary" block @click="login">
          Login
        </v-btn>
      </v-card-text>
    </v-card>

    <!-- Simple Snackbar -->
    <v-snackbar v-model="snackbar" color="success">
      Successfully logged in!
    </v-snackbar>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

definePageMeta({
  layout: 'login'
})

const router = useRouter()
const snackbar = ref(false)

const login = () => {
  // Show snackbar
  snackbar.value = true
  
  // Redirect after 1 second
  setTimeout(() => {
    router.push('/dashboard')  // ✅ Use router, not $router
  }, 1000)
}
</script>