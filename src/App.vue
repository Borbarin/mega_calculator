<script setup>
import {compile, computed, ref} from 'vue'
import CalcButton from './components/CalcButton.vue'

// Значение на экране
const display = ref('0')
const expression = ref('')
const operatorFlag = ref(false)
const operation = ref('')
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

function calculate(){
  let a = parseFloat(expression.value.slice(0, -1))
  let b = parseFloat(display.value)
  switch (operation.value){
    case '+':
      expression.value = String(a+b)
      break
    case '−':
      expression.value = String(a-b)
      break
    case '÷':
      expression.value = String(a/b)
      break
    case '×':
      expression.value = String(a*b)
      break
    default:
      expression.value = 'Error'
      break
  }


}
function funcPress(label){
  switch (label){
    case "M+":
      break
    case "M-":
      break
    case "MR":
      break
    case "MS":
      break
    case "AC":
      display.value = "0"
      expression.value = ''
      operatorFlag.value = false
      operation.value = ''
      break
    case "MC":
      break
    case "%":
      break
  }
}

function operatorPress(label){
  switch (label){
    case "=":
      calculate()
      operatorFlag.value = false
      display.value = expression.value
      expression.value = ''
      break
    default:
      if(operatorFlag.value){
        calculate()
        operation.value = label
        expression.value = expression.value + operation.value
        display.value = '0'
      }
      else{
        expression.value = display.value
        operation.value = label
        display.value = '0'
      }
      operatorFlag.value = true
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

function onButtonPress(label, variant) {
  switch (variant){
    case "number":
      if (display.value.length<15) {
        if (display.value === "0") {
          display.value = label
        } else {
          display.value += label
        }
      }
      break
    case "function":
      funcPress(label)
      break
    case "operator":
      if (display.value.length<16){
        display.value += label
      }
      operatorPress(label)
      break
  }
}
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