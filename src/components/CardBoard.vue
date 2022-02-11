<script>
import Card from "./Card.vue";

export default {
  components: {
    Card,
  },
  props: ["actualBoard", "selectedCard"],
  data() {
    return {
      id: 0,
      cards: [],
    };
  },
  methods: {
    addCard(card) {
      if (this.cards.length < 6) {
        card.id = this.id;
        const newCard = card;
        this.cards.push(newCard);
        ++this.id;
        this.$emit(
          "updateBoardFull",
          this.actualBoard,
          this.cards.length === 6
        );
        return newCard;
      }
    },
    selectCard(event, card) {
      if (event.target.nodeName === "DIV") {
        this.$emit("addSelectedCard", card);
      }
    },
    setCardColor(id, color, noEmit = false) {
      for (const card of this.cards) {
        if (card.id === id) {
          card.color = color;
          if (!noEmit && card.clone.id !== undefined) {
            this.$emit(
              "updateReferenceCard",
              card.clone.id,
              color,
              card.clone.position
            );
          }
          return true;
        }
      }
      return false;
    },
    deleteReferenceCard(id) {
      for (const card of this.cards) {
        if (card.id === id) {
          card.clone = {};
          break;
        }
      }
    },
    setCardClone(id, clone) {
      for (const card of this.cards) {
        if (card.id === id) {
          card.clone = clone;
          break;
        }
      }
    },
    deleteCard(card, deleteReference = true) {
      this.cards = this.cards.filter((c) => c.id !== card.id);
      if (deleteReference && card.clone.id !== undefined) {
        this.$emit("deleteReferenceCard", card.clone.id, this.actualBoard);
      }
      this.$emit("updateBoardFull", this.actualBoard, this.cards.length === 6);
    },
  },
};
</script>

<template>
  <div class="card-board">
    <button
      @click="addCard({ color: '', clone: {}, position: this.actualBoard })"
      :class="[this.cards.length >= 6 ? 'disabled' : '']"
    >
      +
    </button>
    <Card
      v-for="card in cards"
      :key="card.id"
      :id="card.id"
      :color="card.color"
      :isSelected="card === this.selectedCard"
      @onChangeCardColor="setCardColor"
      @click="selectCard($event, card)"
      @deleteCard="deleteCard(card)"
    />
  </div>
</template>

<style>
@import "../assets/cardBoard.css";
</style>
