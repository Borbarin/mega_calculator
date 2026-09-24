<script setup>
import { computed, ref } from 'vue'
import CalcButton from './components/CalcButton.vue'

const display = ref('0')
const expression = ref('')
const previousValue = ref(null)
const operation = ref(null)
const waitingForNewValue = ref(false)
const buttons = [
  {label: 'M+', variant: 'function'},
  {label: 'M-', variant: 'function'},
  {label: 'MR', variant: 'function'},
  {label: 'MS', variant: 'function'},

  {label: 'AC', variant: 'function'},
  {label: 'MC', variant: 'function'},
  {label: '%', variant: 'function'},
  {label: '÷', variant: 'operator'},

  {label: '7', variant: 'number'},
  {label: '8', variant: 'number'},
  {label: '9', variant: 'number'},
  {label: '×', variant: 'operator'},

  {label: '4', variant: 'number'},
  {label: '5', variant: 'number'},
  {label: '6', variant: 'number'},
  {label: '−', variant: 'operator'},

  {label: '1', variant: 'number'},
  {label: '2', variant: 'number'},
  {label: '3', variant: 'number'},
  {label: '+', variant: 'operator'},

  {label: '0', variant: 'number', wide: true},
  {label: ',', variant: 'number'},
  {label: '=', variant: 'operator'}
]

function updateExpression() {
  if (previousValue.value !== null && operation.value !== null) {
    const prevStr = String(previousValue.value).replace('.', ',').replace('-', '−')
    expression.value = `${prevStr} ${operation.value}`
  } else {
    expression.value = ''
  }
}

function calculate() {
  if (previousValue.value === null || operation.value === null) return

  const currentStr = display.value.replace(',', '.').replace('−', '-')
  const current = parseFloat(currentStr)
  const safeCurrent = isNaN(current) ? 0 : current
  let result = 0

  switch (operation.value) {
    case '+':
      result = previousValue.value + safeCurrent
      break
    case '−':
      result = previousValue.value - safeCurrent
      break
    case '×':
      result = previousValue.value * safeCurrent
      break
    case '÷':
      result = safeCurrent === 0 ? 'Error' : previousValue.value / safeCurrent
      break
  }

  if (result === 'Error') {
    display.value = 'Error'
    previousValue.value = null
    operation.value = null
    waitingForNewValue.value = true
    expression.value = ''
    return
  }

  const cleanResult = parseFloat(result.toPrecision(15))
  display.value = String(cleanResult).replace('.', ',').replace('-', '−')
  previousValue.value = cleanResult
}

function onNumberPress(label) {
  if (waitingForNewValue.value) {
    if (display.value === '−' || display.value === '-') {
      display.value = '−' + label
    } else {
      display.value = label
    }
    waitingForNewValue.value = false
  } else {
    if (display.value === '0') {
      display.value = label === ',' ? '0,' : label
    } else if (display.value === '−' || display.value === '-') {
      display.value = label === ',' ? '−0,' : '−' + label
    } else {
      display.value += label
    }
  }
}

function onOperatorPress(label) {
  if (display.value === 'Error') return

  if (label === '=') {
    if (operation.value !== null && previousValue.value !== null) {
      calculate()
    }
    operation.value = null
    previousValue.value = null
    waitingForNewValue.value = true
    expression.value = ''
    return
  }

  if (waitingForNewValue.value) {
    if (label === '−') {
      display.value = '−'
      return
    } else {
      operation.value = label
      updateExpression()
      return
    }
  }

  const currentStr = display.value.replace(',', '.').replace('−', '-')
  const currentValue = parseFloat(currentStr)
  const safeCurrentValue = isNaN(currentValue) ? 0 : currentValue

  if (previousValue.value === null) {
    previousValue.value = safeCurrentValue
  } else {
    calculate()
  }

  operation.value = label
  waitingForNewValue.value = true
  updateExpression()
}

function onFunctionPress(label) {
  switch (label) {
    case 'AC':
      display.value = '0'
      expression.value = ''
      previousValue.value = null
      operation.value = null
      waitingForNewValue.value = false
      break

    case '%':
      if (display.value === 'Error') return;

      const currentStr = display.value.replace(',', '.').replace('−', '-');
      const current = parseFloat(currentStr);
      if (isNaN(current)) return;

      if (previousValue.value !== null && operation.value !== null) {
        let result = 0;


        if (operation.value === '×' || operation.value === '÷') {
          result = previousValue.value * (current / 100);
        }

        else if (operation.value === '+') {
          result = previousValue.value + (previousValue.value * (current / 100));
        }

        else if (operation.value === '−') {
          result = previousValue.value - (previousValue.value * (current / 100));
        }

        const cleanResult = parseFloat(result.toPrecision(15));
        display.value = String(cleanResult).replace('.', ',').replace('-', '−');


        previousValue.value = cleanResult;
        operation.value = null;
        expression.value = '';
        waitingForNewValue.value = true;
      } else {

        const result = current / 100;
        const cleanResult = parseFloat(result.toPrecision(15));
        display.value = String(cleanResult).replace('.', ',').replace('-', '−');
        waitingForNewValue.value = true;
      }
      break;

    case 'MC':
      localStorage.clear('memory')
      break;
    case 'M+':
      if(localStorage.getItem !== ''){
         localStorage.setItem('memory', String(parseFloat(localStorage.getItem('memory')) + parseFloat(display.value)))
      }
      break;
    case 'M-':
      if(localStorage.getItem !== ''){
        localStorage.setItem('memory', String(parseFloat(localStorage.getItem('memory')) - parseFloat(display.value)))
      }
      break;
    case 'MR':
      display.value = localStorage.getItem('memory')
      break;
    case 'MS':
      localStorage.setItem('memory', display.value)
      break;
  }
}

function onButtonPress(label, variant) {
  if (display.value === 'Error') {
    if (label === 'AC') {
      onFunctionPress('AC')
    }
    return
  }

  switch (variant) {
    case "number":
      onNumberPress(label)
      break
    case "function":
      onFunctionPress(label)
      break
    case "operator":
      onOperatorPress(label)
      break
  }
}

const displayFontSize = computed(() => {
  const len = display.value.length
  if (len <= 6)  return '5rem'
  if (len <= 9) return '4rem'
  if (len <= 12) return '3rem'
  if (len <= 15) return '2.5rem'
  return '2.2rem'
})
</script>

<template>
  <div class="calculator">
    <div class="expression" :style="{fontSize:displayFontSize}">{{ expression }}</div>
    <div class="display" :style="{fontSize:displayFontSize}">{{ display }}</div>

    <div class="buttons-grid">
      <CalcButton
          v-for="btn in buttons"
          :key="btn.label"
          :label="btn.label"
          :variant="btn.variant"
          :class="{ 'calc-btn--wide': btn.wide }"
          @press="onButtonPress"
      />
    </div>
  </div>
</template>

<style scoped>
.calculator {
  width: 380px;
  height: 760px;
  margin: 40px auto;
  padding: 24px 16px;
  background: #000000;
  border-radius: 40px;
  font-family: -apple-system, 'SF Pro Display', 'Helvetica Neue', sans-serif;
}

.display {
  height: 170px;
  color: #ffffff;
  font-weight: 300;
  text-align: right;
  padding: 20px 12px 30px;
  overflow: hidden;
  white-space: nowrap;

  display: flex;
  align-items: flex-end;
  justify-content: flex-end;
}

.buttons-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
  justify-items: center;
}
</style>