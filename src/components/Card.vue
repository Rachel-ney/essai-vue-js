<script>
export default {
  props: ["id", "color", "isSelected"],
};
</script>

<template>
  <div
    :class="[isSelected ? 'card selected' : 'card']"
    :style="{ backgroundColor: !color ? 'White' : color }"
  >
    <button @click="$emit('deleteCard')">X</button>
    <input
      type="text"
      v-model="color"
      @input="$emit('onChangeCardColor', this.id, color)"
    />
  </div>
</template>

<style>
@import "../assets/card.css";
</style>
