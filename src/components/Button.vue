<script setup>
  defineProps({
    label: {
      type: String,
      required: true
    },
    inputType: {
      type: String,
      required: false,
      default: 'button'
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
    },
    height: {
      type: String,
      required: false,
      default: '50px'
    }
  })
</script>

<template>
  <button
    class="Button"
    type="button"
    :style="{'--ButtonBgColor':color,'--ButtonTextColor':textColor, '--ButtonHeight':height}"
    @click="$emit('click-button')"
  >
    <span class="color">{{ label }}</span>
  </button>
</template>

<style scoped>
  .Button {
    display: flex;
    align-items: center;
    justify-content: center;
    width: auto;
    padding: 16px 24px;
    font-size: 1rem;
    font-weight: bold;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    color: var(--ButtonTextColor);
    background-color: var(--ButtonBgColor);
    height: var(--ButtonHeight);
  }

  .Button:hover {
    opacity: 90%;
  }
</style>
