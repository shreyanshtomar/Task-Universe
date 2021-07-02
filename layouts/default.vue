<template>
  <v-app>
    <v-container>
      <v-app-bar absolute color="#212529" elevate-on-scroll>
        <v-toolbar-title class="toolbar">
          <v-icon style="color: white; margin-right: 10px" large>
            mdi-book-open-outline
          </v-icon>
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
  display: flex;
  .toolbar-icon {
    width: 20px;
    padding: 2px;
    margin-right: 10px;
  }
  .toolbar-title {
    padding-top: 3px;
    color: white;
  }
}

.v-main {
  background-color: #adb5bd;
  overflow: scroll;
  overflow-y: hidden;
  margin-top: 25px;
}
</style>
