<template>
  <div class="d-flex flex-column board">
    <div class="d-flex flex-row pr-6 pt-3">
      <div
        v-for="list in DATA.lists"
        class="d-flex flex-column pt-3 mr-6 list"
        :key="list.listId"
      >
        <div class="d-flex flex-row justify-space-between">
          <div>{{ list.list.title }} {{ list.list.cards.length }}</div>
          <v-icon
            small
            @click="deleteList(list.listId)"
            style="margin-right: 10px, margin-left:30px"
            >mdi-delete-empty-outline</v-icon
          >
        </div>

        <!--                 display cards              -->
        <v-card
          v-for="card in list.list.cards"
          :draggable="true"
          @dragover.prevent
          @dragstart="drag($event, card)"
          class="mt-5 card"
          @click="editCard(card)"
          v-bind:key="card.id"
        >
          <v-card-text>
            {{ card.title }}
          </v-card-text>
        </v-card>

        <!--  *****************   ADD NEW TASK with DIALOG BOX   ************************ -->
        <v-container class="new-task">
          <v-btn
            depressed
            @click="
              dialogCard = true;
              listId = list.listId;
            "
            class="mt-auto"
            style="
              width: 250px;
              display: flex;
              flex-direction: column;
              align-items: flex-start;
            "
          >
            + New
          </v-btn>
        </v-container>
      </div>
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

      <!--  *****************   Dialog and Button for Status ************************ -->
      <div class="d-flex flex-row">
        <v-btn depressed @click="dialog = true" class="create-list"
          >Create new Status</v-btn
        >
        <v-dialog v-model="dialog" persistent max-width="600px">
          <v-card elevation="0">
            <v-card-title>
              <span class="headline">What's Your Status?</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12">
                    <v-text-field
                      label="Whatever you're upto!"
                      v-model="list.title"
                      outlined
                      required
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog = false">
                Close
              </v-btn>
              <v-btn color="blue darken-1" text @click="createList()">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>

      <!-- **************   Dialog For Edit ***************  -->

      <v-dialog v-model="dialogEditCard" persistent max-width="600px">
        <v-card elevation="0">
          <v-card-title>
            <span class="headline">{{ currentCard.title }}</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    label="Edit title"
                    v-model="currentCard.title"
                    required
                  ></v-text-field>
                  <v-text-field
                    label="Edit description"
                    v-model="currentCard.description"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="red darken-1" text @click="deleteCard()">
              Delete
            </v-btn>
            <v-btn color="blue darken-1" text @click="dialogEditCard = false">
              Close
            </v-btn>
            <v-btn color="blue darken-1" text @click="updateCard()">
              Save
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import { v4 as uuidv4 } from "uuid";
import DATA from "../assets/lists";

export default {
  mounted() {
    DATA;
  },

  data() {
    return {
      DATA: DATA,
      listId: "",
      list: {
        title: ""
      },
      card: {
        cardId: "",
        title: "",
        description: ""
      },
      currentCard: {},
      cardDraggedId: "",
      cardDraggedListId: "",
      dialog: false,
      dialogCard: false,
      dialogEditCard: false
    };
  },

  methods: {
    async createList() {
      this.dialog = false;
      await console.log("before", DATA);

      if (this.list.title !== "") {
        this.listId = uuidv4();
        this.list.cards = [];
        if (this.DATA.lists) {
          DATA.lists.push({
            listId: this.listId,
            list: { title: this.list.title, cards: this.list.cards }
            //cards: this.list.cards
          });
        } else {
          this.DATA.lists = [];
          DATA.lists.push({
            listId: this.listId,
            list: { title: this.list.title, cards: this.list.cards }

            //            cards: this.list.cards
          });
        }

        console.log("after", DATA);
        this.list = {};
      }
    },

    async deleteList(listId) {
      let that = this;
      let index = -1;
      let count = 0;
      for (const list of that.DATA.lists) {
        if (list.listId === listId) {
          index = count;
        }
        count++;
      }
      if (index > -1) {
        that.DATA.lists.splice(index, 1);
      }
    },

    async createCard() {
      let that = this;
      console.log(this.list.cards);
      that.dialogCard = false;

      if (that.card.title !== "") {
        that.card.cardId = uuidv4();
        that.card.listId = that.listId;

        if (that.DATA.lists) {
          let index = -1;
          let count = 0;
          for (const list of that.DATA.lists) {
            if (list.listId === that.listId) {
              index = count;
            }
            count++;
          }
          if (index !== -1) {
            if (that.DATA.lists[index].list.cards) {
              console.log("*******", that.card);
              that.DATA.lists[index].list.cards.push(that.card);

              console.log(
                "BYEEEEEEEEEEEEEEEee",
                JSON.stringify(that.DATA.lists)
              );
            } else {
              that.DATA.lists[index].list.cards = [];
              that.DATA.lists[index].list.cards.push(that.card);
            }
          }
        }
      }
      that.card = [];
      that.listId = "";
    },

    editCard(card) {
      this.dialogEditCard = true;
      this.currentCard = card;
    },

    async deleteCard() {
      let that = this;
      that.dialogEditCard = false;
      let i = 0;
      let j = 0;
      let index = {
        list: -1,
        card: -1
      };
      console.log("cardcurr: ", that.currentCard);
      for (const list of that.DATA.lists) {
        if (that.currentCard.listId === list.listId) {
          //correct list, now find card
          for (const card of list.list.cards) {
            if (card.cardId === that.currentCard.cardId) {
              index.list = i;
              index.card = j;
            }
            j++;
          }
        }
        i++;
      }
      //console.log("index of card: " + index.card);

      if (index.list > -1) {
        that.DATA.lists[index.list].list.cards.splice(index.card, 1);
      }
    },

    async updateCard() {
      this.dialogEditCard = false;
      // for (const list of this.DATA.lists) {
      //   if (this.currentCard.listId === list.id) {
      //     //correct list, now find card
      //     for (const card of list.list.cards) {
      //       if (card.id === this.currentCard.id) {
      //         card = this.currentCard;
      //       }
      //     }
      //   }
      // }
    }
  }
};
</script>

<style lang="scss" scoped>
.board {
  padding-left: 45px;

  .list {
    width: 250px;

    .v-card__text {
      padding: 12px;
    }
  }
  .new-task {
    display: flex;
    flex-direction: row;
    padding-left: 0;
    opacity: 0.5;
  }
}
</style>
