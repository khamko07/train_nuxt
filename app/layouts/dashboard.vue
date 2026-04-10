<template>
  <v-layout>
    <v-app-bar color="blue-darken-3" elevation="2" flat>
      <v-app-bar-nav-icon @click="rail = !rail"></v-app-bar-nav-icon>
      <v-toolbar-title class="font-weight-bold">HR Management</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-chip color="blue-lighten-4" prepend-icon="mdi-account-circle" class="mr-3">
        Admin
      </v-chip>
      <v-btn icon variant="text" @click="logout" title="Logout">
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" :rail="rail" permanent>
      <v-list-item
        prepend-icon="mdi-domain"
        title="HR System"
        nav
      >
        <template #append>
          <v-btn
            :icon="rail ? 'mdi-chevron-right' : 'mdi-chevron-left'"
            variant="text"
            @click="rail = !rail"
          ></v-btn>
        </template>
      </v-list-item>

      <v-divider></v-divider>

      <v-list density="compact" nav>
        <v-list-item
          prepend-icon="mdi-account-group-outline"
          title="Employees"
          to="/dashboard/employees"
          value="employees"
        ></v-list-item>
      </v-list>

      <template #append>
        <v-divider></v-divider>
        <v-list density="compact" nav>
          <v-list-item
            prepend-icon="mdi-logout"
            title="Logout"
            @click="logout"
          ></v-list-item>
        </v-list>
      </template>
    </v-navigation-drawer>

    <v-main>
      <v-container fluid class="pa-6">
        <slot />
      </v-container>
    </v-main>
  </v-layout>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(true)
const rail = ref(false)

function logout() {
  navigateTo('/login')
}
</script>
