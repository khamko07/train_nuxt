<template>
  <div class="w-100 d-flex justify-center">
    <v-card
      class="mx-auto pa-12 pb-8"
      elevation="3"
      max-width="448"
      rounded="lg"
    >
      <div class="text-subtitle-1 font-weight-bold mb-1">Create Account</div>
      <div class="text-body-2 text-medium-emphasis mb-6">Fill in the form below to register</div>

      <v-text-field
        v-model="form.name"
        density="compact"
        placeholder="Full Name"
        prepend-inner-icon="mdi-account-outline"
        variant="outlined"
        :error-messages="errors.name"
        class="mb-1"
      ></v-text-field>

      <v-text-field
        v-model="form.email"
        density="compact"
        placeholder="Email address"
        prepend-inner-icon="mdi-email-outline"
        variant="outlined"
        :error-messages="errors.email"
        class="mb-1"
      ></v-text-field>

      <v-text-field
        v-model="form.password"
        :append-inner-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
        :type="showPassword ? 'text' : 'password'"
        density="compact"
        placeholder="Password"
        prepend-inner-icon="mdi-lock-outline"
        variant="outlined"
        :error-messages="errors.password"
        class="mb-1"
        @click:append-inner="showPassword = !showPassword"
      ></v-text-field>

      <v-text-field
        v-model="form.confirmPassword"
        :type="'password'"
        density="compact"
        placeholder="Confirm Password"
        prepend-inner-icon="mdi-lock-check-outline"
        variant="outlined"
        :error-messages="errors.confirmPassword"
      ></v-text-field>

      <v-btn
        class="mb-8 mt-2"
        color="blue"
        size="large"
        variant="tonal"
        block
        @click="register"
      >
        Register
      </v-btn>

      <v-card-text class="text-center pt-0">
        Already have an account?
        <NuxtLink class="text-blue text-decoration-none" to="/login">
          Log in <v-icon icon="mdi-chevron-right"></v-icon>
        </NuxtLink>
      </v-card-text>
    </v-card>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const showPassword = ref(false)

const form = ref({
  name: '',
  email: '',
  password: '',
  confirmPassword: '',
})

const errors = ref({
  name: '',
  email: '',
  password: '',
  confirmPassword: '',
})

function validate() {
  let valid = true
  errors.value = { name: '', email: '', password: '', confirmPassword: '' }

  if (!form.value.name.trim()) {
    errors.value.name = 'Full name is required'
    valid = false
  }
  if (!form.value.email.trim()) {
    errors.value.email = 'Email is required'
    valid = false
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email)) {
    errors.value.email = 'Invalid email address'
    valid = false
  }
  if (!form.value.password) {
    errors.value.password = 'Password is required'
    valid = false
  } else if (form.value.password.length < 6) {
    errors.value.password = 'Password must be at least 6 characters'
    valid = false
  }
  if (!form.value.confirmPassword) {
    errors.value.confirmPassword = 'Please confirm your password'
    valid = false
  } else if (form.value.password !== form.value.confirmPassword) {
    errors.value.confirmPassword = 'Passwords do not match'
    valid = false
  }

  return valid
}

function register() {
  if (validate()) {
    navigateTo('/login')
  }
}
</script>
