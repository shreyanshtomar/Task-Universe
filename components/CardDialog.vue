<template>
  <v-dialog v-model="dialogCard" persistent max-width="600px">
    <v-card elevation="0">
      <v-card-title>
        <span class="headline">Card name</span>
      </v-card-title>
      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-text-field
                label="Stuff to do"
                v-model="card.title"
                rounded
                filled
                required
              ></v-text-field>
              <v-text-field
                label="Description"
                v-model="card.description"
                outlined
                required
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="dialogCard = false">
          Close
        </v-btn>
        <v-btn color="blue darken-1" text @click="createCard()">
          Save
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {}
};
</script>

<style lang="scss" scoped></style>
