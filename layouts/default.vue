<template>
  <v-app>
    <v-container>
      <v-app-bar absolute color="#343a40" elevate-on-scroll>
        <v-app-bar-nav-icon></v-app-bar-nav-icon>

        <v-toolbar-title class="toolbar">
          <!-- <div class="toolbar-icon">
            <img :src="require('../assets/task.svg')" alt="task-icon" />
          </div> -->
          <div class="toolbar-title">
            Task Universe
          </div>
        </v-toolbar-title>

        <v-spacer></v-spacer>

        <v-btn style="color: white" icon>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </v-app-bar>
    </v-container>

    <v-main>
      <Nuxt />
    </v-main>
  </v-app>
</template>

<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400;500&display=swap");

.toolbar {
  font-family: "Quicksand", sans-serif;
  font-weight: 500;
  color: white;
  display: flex;
  .toolbar-icon {
    width: 20px;
    padding: 2px;
    margin-right: 10px;
  }
}

.v-main {
  background-color: #f1faee;
  overflow: scroll;
  margin-top: 25px;
}
</style>
