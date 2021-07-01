<template>
  <v-app>
    <v-container>
      <v-app-bar absolute color="white" elevate-on-scroll>
        <v-app-bar-nav-icon></v-app-bar-nav-icon>

        <v-toolbar-title>Task Universe</v-toolbar-title>

        <v-spacer></v-spacer>

        <v-btn icon>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </v-app-bar>
    </v-container>

    <v-main>
      <Nuxt />
    </v-main>
  </v-app>
</template>

<style scoped>
.v-main {
  margin-top: 25px;
}
</style>
