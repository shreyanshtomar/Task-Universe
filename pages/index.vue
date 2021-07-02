<template>
  <div class="d-flex flex-column board">
    <TitleHeader />
    <div class="d-flex flex-row pr-6 pt-3" style="min-height: 800px;">
      <div class="choose-img">
        <img
          src="../assets/choose.svg"
          alt="icon"
          class="task-icon"
          style="opacity: 0.5; height:350px;"
        />
      </div>
      <div
        v-for="list in DATA.lists"
        class="d-flex flex-column pt-3 mr-6 list"
        @drop="drop($event, list.listId)"
        @dragover="allowDrop($event)"
        :key="list.listId"
      >
        <div class="d-flex flex-row justify-space-between">
          <div style="font-weight: 500" class="list-head">
            <div class="list-title">
              {{ list.list.title }}
            </div>
            <v-tooltip top>
              <template v-slot:activator="{ on, attrs }">
                <div v-bind="attrs" v-on="on">
                  <div class="total-card" style="opacity: 0.5;">
                    : {{ list.list.cards.length }}
                  </div>
                </div>
              </template>
              <span>Number of Cards</span>
            </v-tooltip>
          </div>

          <v-tooltip top>
            <template v-slot:activator="{ on, attrs }">
              <v-icon
                small
                @click="deleteList(list.listId)"
                style="margin-right: 10px, margin-left:30px"
                v-bind="attrs"
                v-on="on"
              >
                mdi-delete-empty-outline
              </v-icon>
            </template>
            <span>Delete Status</span>
          </v-tooltip>
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
          elevation="4"
        >
          <v-card-text style="font-weight: 500">
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
            class="mt-auto btn-new"
            style="
              width: 90px;
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
                    outlined
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

      <!--  *****************   Button and Dialog for New Status ************************ -->
      <div class="d-flex flex-row">
        <v-dialog v-model="dialog" persistent max-width="600px">
          <v-card elevation="2">
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
        <div style="padding-right: 40px">
          <v-btn
            depressed
            @click="dialog = true"
            class="create-list"
            elevation="4"
            style="background-color: #495057; color: white; "
            >+ New</v-btn
          >
        </div>
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
      dialogEditCard: false,
      drawer: false
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
          });
        } else {
          this.DATA.lists = [];
          DATA.lists.push({
            listId: this.listId,
            list: { title: this.list.title, cards: this.list.cards }
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
              that.DATA.lists[index].list.cards.push(that.card);
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

      if (index.list > -1) {
        that.DATA.lists[index.list].list.cards.splice(index.card, 1);
      }
    },

    async updateCard() {
      this.dialogEditCard = false;
    },

    async updateCardList(newlistId) {
      let that = this;
      let tempListIndex = -1;
      let tempCardIndex = -1;
      let newListIndex = -1;
      let tempListCount = 0;
      let tempCardCount = 0;

      //get the index in current cards current list
      for (const list of that.DATA.lists) {
        console.log("list.listId:", list.listId, "new list id: ", newlistId);

        if (list.listId === newlistId) {
          newListIndex = tempListCount;
          console.log("newListIndex", newListIndex);
        }
        if (that.currentCard.listId === list.listId) {
          //correct list, now find card
          tempListIndex = tempListCount;
          for (const card of list.list.cards) {
            if (card.cardId === that.currentCard.cardId) {
              tempCardIndex = tempCardCount;
              break;
            }
            tempCardCount++;
          }
        }
        tempListCount++;
      }

      //remove currentCard from current list
      console.log("CARDS-BEFORE: ", that.DATA.lists[tempListIndex].list.cards);

      that.DATA.lists[tempListIndex].list.cards.splice(tempCardIndex, 1);

      console.log(
        "CARDS-AFTER: ",
        that.DATA.lists[tempListIndex].list.cards,
        "TEMP CARD IDX:",
        tempCardIndex,
        "TEMP LIST IDX: ",
        tempListIndex
      );

      //add currentCard to its new list
      that.currentCard.listId = newlistId;
      that.DATA.lists[newListIndex].list.cards.push(that.currentCard);
    },

    allowDrop(ev) {
      ev.preventDefault();
    },
    drag(ev, card) {
      this.currentCard = card;
      console.log("current card:", this.currentCard);
    },
    drop(ev, listId) {
      ev.preventDefault();
      console.log("listId: ", listId);
      this.updateCardList(listId);
    }
  }
};
</script>

<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400;500&display=swap");

.board {
  font-family: "Quicksand", sans-serif;
  padding-left: 150px;
  width: 100%;

  .create-list {
    background: none;
  }

  .list {
    min-width: 250px;

    .v-card__text {
      padding: 12px;
    }

    .list-head {
      display: flex;

      .list-title {
        border-radius: 10px;
        padding: 0px 2px;
      }
    }
  }
  .new-task {
    display: flex;
    flex-direction: row;
    padding-left: 0;

    .btn-new {
      opacity: 0.5;
      background: none;
    }

    .btn-new:hover {
      background-color: #6c757d;
    }
  }
}
</style>
