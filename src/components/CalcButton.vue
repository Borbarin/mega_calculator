<script setup>
const props = defineProps({
  label: {
    type: String,
    required: true
  },
  variant: {
    type: String,
    default: 'number'
  }
})

const emit = defineEmits(['press'])

function handleClick() {
  emit('press', props.label, props.variant)
}
</script>

<template>
  <button
      class="calc-btn"
      :class="`calc-btn--${variant}`"
      @click="handleClick"
  >
    {{ label }}
  </button>
</template>

<style scoped>
.calc-btn {
  border: none;
  border-radius: 999px;
  font-size: 1.8rem;
  font-weight: 400;
  height: 76px;
  width: 76px;
  cursor: pointer;
  transition: filter 0.15s ease, transform 0.05s ease;
  user-select: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

.calc-btn:active {
  filter: brightness(1.9);
  transform: scale(0.9);
}

.calc-btn--number {
  background: #333333;
  color: #ffffff;
}

.calc-btn--number:hover {
  filter: brightness(1.2);
}

.calc-btn--operator {
  background: #ff9f0a;
  color: #ffffff;
}

.calc-btn--operator:hover {
  filter: brightness(1.1);
}

.calc-btn--function {
  background: #a5a5a5;
  color: #000000;
}

.calc-btn--function:hover {
  filter: brightness(1.1);
}

.calc-btn--wide {
  grid-column: span 2;
  width: 100%;
  justify-content: center;
}
</style>