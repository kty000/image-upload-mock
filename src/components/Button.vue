<script setup>
  defineProps({
    label: {
      type: String,
      required: true
    },
    color: {
      type: String,
      required: false,
      default: '#103e6b'
    },
    textColor: {
      type: String,
      required: false,
      default: '#fff'
    }
  })
</script>

<template>
  <button
    class="Button"
    type="button"
    :style="{'--ButtonBgColor':color,'--ButtonTextColor':textColor}"
    @click="$emit('click-button')">
    <span class="color">{{ label }}</span>
  </button>
</template>

<style scoped>
  .Button {
    display: block;
    width: auto;
    padding: 16px 24px;
    font-size: 1rem;
    font-weight: bold;
    text-align: center;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    color: var(--ButtonTextColor);
    background-color: var(--ButtonBgColor);
  }

  .Button:hover {
    opacity: 90%;
  }
</style>
