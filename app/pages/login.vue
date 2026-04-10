<template>
  <div class="w-100 d-flex justify-center">
    <v-card
      class="mx-auto pa-12 pb-8"
      elevation="3"
      max-width="448"
      rounded="lg"
    >
      <div class="text-body-large text-medium-emphasis">Account</div>

      <v-text-field
        v-model="email"
        density="compact"
        placeholder="Email address"
        prepend-inner-icon="mdi-email-outline"
        variant="outlined"
      ></v-text-field>

      <div class="text-body-large text-medium-emphasis d-flex align-center justify-space-between">
        Password

        <a
          class="text-body-small text-decoration-none text-blue"
          href="#"
          rel="noopener noreferrer"
          target="_blank"
        >
          Forgot login password?</a>
      </div>

      <v-text-field
        v-model="password"
        :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
        :type="visible ? 'text' : 'password'"
        density="compact"
        placeholder="Enter your password"
        prepend-inner-icon="mdi-lock-outline"
        variant="outlined"
        @click:append-inner="visible = !visible"
      ></v-text-field>

      <v-card
        class="mb-12"
        color="surface-variant"
        variant="tonal"
      >
      </v-card>

      <v-btn
        class="mb-8"
        color="blue"
        size="large"
        variant="tonal"
        block
        @click="login"
      >
        Log In
      </v-btn>

      <v-card-text class="text-center">
        <NuxtLink class="text-blue text-decoration-none" to="/register">
          Sign up now <v-icon icon="mdi-chevron-right"></v-icon>
        </NuxtLink>
      </v-card-text>
    </v-card>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const visible = ref(false)
const email = ref('')
const password = ref('')

function login() {
  navigateTo('/dashboard')
}
</script>
