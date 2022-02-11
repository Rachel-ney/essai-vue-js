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
      },
      boardFull: {
        leftBoard: false,
        rightBoard: false,
      },
    };
  },
  methods: {
    addSelectedCard(card) {
      if (this.selectedCard.card === card) {
        this.selectedCard.card = {};
      } else {
        this.selectedCard.card = card;
      }
    },
    deleteSelectedCard() {
      if (this.selectedCard.card.id !== undefined) {
        this.$refs[this.selectedCard.card.position].deleteCard(
          this.selectedCard.card
        );
        this.selectedCard.card = {};
      }
    },
    copySelectedCard(byReference = false) {
      if (this.selectedCard.card.id !== undefined) {
        if (byReference && this.selectedCard.card.clone.id !== undefined) {
          return;
        }
        const siblingBoard =
          this.selectedCard.card.position === "leftBoard"
            ? "rightBoard"
            : "leftBoard";
        const newCard = this.$refs[siblingBoard].addCard({
          color: this.selectedCard.card.color,
          clone: byReference ? this.selectedCard.card : {},
          position: siblingBoard,
        });

        if (newCard) {
          if (byReference) {
            this.$refs[this.selectedCard.card.position].setCardClone(
              this.selectedCard.card.id,
              newCard
            );
          }
          this.selectedCard.card = {};
        }
      }
    },
    moveSelectedCard() {
      if (this.selectedCard.card.id !== undefined) {
        const siblingBoard =
          this.selectedCard.card.position === "leftBoard"
            ? "rightBoard"
            : "leftBoard";
        const newCard = this.$refs[siblingBoard].addCard({
          color: this.selectedCard.card.color,
          clone:
            this.selectedCard.card.clone !== undefined
              ? this.selectedCard.card.clone
              : {},
          position: siblingBoard,
        });

        if (newCard) {
          if (this.selectedCard.card.clone.id !== undefined) {
            this.$refs[this.selectedCard.card.clone.position].setCardClone(
              this.selectedCard.card.clone.id,
              newCard
            );
          }

          this.$refs[this.selectedCard.card.position].deleteCard(
            this.selectedCard.card,
            false
          );
          this.selectedCard.card = {};
        }
      }
    },
    updateReferenceCard(id, color, board) {
      this.$refs[board].setCardColor(id, color, true);
    },
    deleteReferenceCard(id, board) {
      this.$refs[board].deleteReferenceCard(id);
    },
    updateBoardFull(actualBoard, isFull) {
      this.boardFull[actualBoard] = isFull;
    },
    isButtonDisabled() {
      if (this.selectedCard.card.id === undefined) {
        return true;
      }

      if (
        this.selectedCard.card.position === "leftBoard" &&
        !this.boardFull.rightBoard
      ) {
        return false;
      }

      if (
        this.selectedCard.card.position === "rightBoard" &&
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
        @click="moveSelectedCard()"
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
