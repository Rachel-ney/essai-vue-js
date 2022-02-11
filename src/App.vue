<script>
import CardBoard from "./components/CardBoard.vue";

export default {
  components: {
    CardBoard,
  },

  data() {
    return {
      selectedCard: {
        card: {},
        actualBoard: "",
        siblingBoard: "",
      },
      boardFull: {
        leftBoard: false,
        rightBoard: false,
      },
    };
  },
  methods: {
    addSelectedCard(card, actualBoard) {
      if (this.selectedCard.card === card) {
        this.resetSelectedCard();
      } else {
        this.resetSelectedCard();
        this.selectedCard.card = card;
        this.selectedCard.actualBoard = actualBoard;
        this.selectedCard.siblingBoard =
          actualBoard === "leftBoard" ? "rightBoard" : "leftBoard";
      }
    },
    deleteSelectedCard() {
      if (this.selectedCard.card.id !== undefined) {
        this.$refs[this.selectedCard.actualBoard].deleteCard(
          this.selectedCard.card
        );
        this.resetSelectedCard();
      }
    },
    copySelectedCard(byReference = false, deleteAfterCopy = false) {
      if (this.selectedCard.card.id !== undefined) {
        if (byReference && this.selectedCard.card.clone.id !== undefined) {
          return;
        }
        const newCard = this.$refs[this.selectedCard.siblingBoard].addCard({
          color: this.selectedCard.card.color,
          clone: byReference && !deleteAfterCopy ? this.selectedCard.card : {},
        });

        if (newCard) {
          if (deleteAfterCopy) {
            this.$refs[this.selectedCard.actualBoard].deleteCard(
              this.selectedCard.card
            );
          }
          if (byReference) {
            this.$refs[this.selectedCard.actualBoard].setCardClone(
              this.selectedCard.card.id,
              newCard
            );
          }
        }

        this.resetSelectedCard();
      }
    },
    updateReferenceCard(id, color, actualBoard) {
      const siblingBoard =
        actualBoard === "leftBoard" ? "rightBoard" : "leftBoard";
      this.$refs[siblingBoard].setCardColor(id, color, true);
    },
    deleteReferenceCard(id, actualBoard) {
      const siblingBoard =
        actualBoard === "leftBoard" ? "rightBoard" : "leftBoard";
      this.$refs[siblingBoard].deleteReferenceCard(id);
    },
    updateBoardFull(actualBoard, isFull) {
      this.boardFull[actualBoard] = isFull;
    },
    resetSelectedCard() {
      this.selectedCard.card = {};
      this.selectedCard.actualBoard = "";
      this.selectedCard.siblingBoard = "";
    },
    isButtonDisabled() {
      if (this.selectedCard.card.id === undefined) {
        return true;
      }

      if (
        this.selectedCard.actualBoard === "leftBoard" &&
        !this.boardFull.rightBoard
      ) {
        return false;
      }

      if (
        this.selectedCard.actualBoard === "rightBoard" &&
        !this.boardFull.leftBoard
      ) {
        return false;
      }

      return true;
    },
  },
};
</script>

<template>
  <div class="container">
    <CardBoard
      ref="leftBoard"
      actualBoard="leftBoard"
      :selectedCard="
        this.selectedCard.card.id !== undefined ? this.selectedCard.card : {}
      "
      @addSelectedCard="addSelectedCard"
      @updateReferenceCard="updateReferenceCard"
      @deleteReferenceCard="deleteReferenceCard"
      @updateBoardFull="updateBoardFull"
    />
    <div class="button-list">
      <button
        :class="this.isButtonDisabled() ? 'disabled' : ''"
        @click="copySelectedCard(false, true)"
      >
        Move
      </button>
      <button
        :class="this.selectedCard.card.id !== undefined ? '' : 'disabled'"
        @click="deleteSelectedCard"
      >
        Delete
      </button>
      <button
        :class="this.isButtonDisabled() ? 'disabled' : ''"
        @click="copySelectedCard()"
      >
        Copy
      </button>
      <button
        :class="
          this.isButtonDisabled() ||
          (this.selectedCard.card.id !== undefined &&
            this.selectedCard.card.clone.id !== undefined)
            ? 'disabled'
            : ''
        "
        @click="copySelectedCard(true)"
      >
        Reference
      </button>
    </div>
    <CardBoard
      ref="rightBoard"
      actualBoard="rightBoard"
      :selectedCard="
        this.selectedCard.card.id !== undefined ? this.selectedCard.card : {}
      "
      @addSelectedCard="addSelectedCard"
      @updateReferenceCard="updateReferenceCard"
      @deleteReferenceCard="deleteReferenceCard"
      @updateBoardFull="updateBoardFull"
    />
  </div>
</template>

<style>
@import "./assets/app.css";
</style>
