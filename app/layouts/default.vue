<template>
  <v-layout class="rounded rounded-md border min-height-screen">
    <v-app-bar title="Application bar"></v-app-bar>

    <v-navigation-drawer>
      <v-list nav>
        <v-list-item title="Login" to="/login" link></v-list-item>
        <v-list-item title="Register" to="/register" link></v-list-item>
        <v-list-item title="About" to="/about" link></v-list-item>
        <v-list-item title="Products" to="/products" link></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <v-container class="fill-height pa-6" fluid>
        <v-sheet
          border="dashed md"
          color="surface-light"
          rounded="lg"
          width="100%"
          class="fill-height pa-6 d-flex align-center justify-center"
          min-height="calc(100vh - 112px)"
        >
          <slot />
        </v-sheet>
      </v-container>
    </v-main>
  </v-layout>
</template>

<style scoped>
.min-height-screen {
  min-height: 100vh;
}
</style>