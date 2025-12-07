<template>
  <div>
    <h1>Dashboard</h1>
    
    <v-row class="mt-4">
      <v-col cols="12" md="3">
        <v-card class="pa-4">
          <div class="text-h6 text-medium-emphasis">Total Items</div>
          <div class="text-h3 font-weight-bold">{{ stats.items }}</div>
          <v-btn color="primary" variant="text" @click="navigateTo('/items')">
            View Items
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4">
          <div class="text-h6 text-medium-emphasis">Total Users</div>
          <div class="text-h3 font-weight-bold">{{ stats.users }}</div>
          <v-btn color="primary" variant="text" @click="navigateTo('/users')">
            View Users
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4">
          <div class="text-h6 text-medium-emphasis">Transactions</div>
          <div class="text-h3 font-weight-bold">{{ stats.transactions }}</div>
          <v-btn color="primary" variant="text" @click="navigateTo('/transactions')">
            View Transactions
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4">
          <div class="text-h6 text-medium-emphasis">Recent Activity</div>
          <div class="text-h3 font-weight-bold">{{ stats.activity }}</div>
          <v-btn color="primary" variant="text" @click="refreshStats">
            Refresh
          </v-btn>
        </v-card>
      </v-col>
    </v-row>
    
    <v-card class="mt-6">
      <v-card-title>Recent Activity</v-card-title>
      <v-card-text>
        <v-data-table :items="recentActivity" :headers="activityHeaders">
          <template v-slot:[`item.time`]="{ item }">
            {{ formatTime(item.time) }}
          </template>
        </v-data-table>
      </v-card-text>
    </v-card>
    
    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<script setup>
const stats = ref({
  items: 0,
  users: 0,
  transactions: 0,
  activity: 0
})

const recentActivity = ref([])
const snackbar = ref(false)
const snackbarColor = ref('')
const snackbarText = ref('')

const activityHeaders = [
  { title: 'Time', key: 'time' },
  { title: 'Action', key: 'action' },
  { title: 'Module', key: 'module' },
  { title: 'User', key: 'user' }
]

const loadDashboardData = async () => {
  try {
    // TODO: Connect to Strapi at school
    // GET: ${config.public.apiUrl}/items/count
    // GET: ${config.public.apiUrl}/users/count
    // GET: ${config.public.apiUrl}/transactions/count
    
    // For now, use mock data
    stats.value = {
      items: 156,
      users: 24,
      transactions: 342,
      activity: 12
    }
    
    recentActivity.value = [
      { id: 1, time: new Date(), action: 'Item created', module: 'Items', user: 'Admin' },
      { id: 2, time: new Date(), action: 'User updated', module: 'Users', user: 'Staff' }
    ]
    
  } catch (err) {
    console.error('Error loading dashboard:', err)
    snackbarColor.value = 'error'
    snackbarText.value = 'Failed to load dashboard data'
    snackbar.value = true
  }
}

const refreshStats = () => {
  loadDashboardData()
  snackbarColor.value = 'success'
  snackbarText.value = 'Dashboard refreshed'
  snackbar.value = true
}

const formatTime = (date) => {
  return new Date(date).toLocaleTimeString()
}

onMounted(() => {
  loadDashboardData()
})
</script>