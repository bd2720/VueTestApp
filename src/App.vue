<!-- JS goes here -->
<script setup>
  import { ref, computed } from 'vue'
  // first operand
  const n1 = ref(0)
  // current operation
  var currOp = undefined
  // second operand
  const n2 = ref(undefined)
  // all operations
  const operations = ['+', '-', 'x', '÷', '%', '&', '|', '^']

  // whether entering n2 or not
  const second = ref(false)
  // whether displaying result of a computation
  var displayingRes = false

  // active base
  const base = ref(10)
  // string rep. of opposite base, for display on button
  const baseStr = computed(() => {
    return (base.value == 2) ? 'Decimal' : 'Binary'
  })
  // digits, 0 to base-1
  const digits = computed(() => {
    return Array.from(Array(base.value).keys())
  })

  // displayed number
  const nDisplayed = computed(() => {
    // show n2 if it is required and exists
    if(second.value && n2.value != undefined) {
      return n2.value.toString(base.value)
    }
    return n1.value.toString(base.value)
  })

  // try to add digit to num
  function addDigit(i) {
    if(second.value){ // add to n2 if n2 required
      n2.value = (n2.value == undefined) ? i : base.value * n2.value + i
    } else { // add to n1
      // if displaying computation result, overwrite
      if(displayingRes){
        n1.value = i
        displayingRes = false
      } else {
        n1.value = base.value * n1.value + i
      }
    }
  }
  // choose operation
  function chooseOp(op){
    // evaluate previous if n2 exists
    if(second.value && n2.value != undefined){
      evaluate()
    }
    // set operation
    currOp = op
    // now we need n2
    second.value = true
    // set n2 value undefined
    n2.value = undefined
  }
  // evaluate n1 `currOp` n2
  function evaluate(){
    // stop inputting n2
    second.value = false
    // if n2 is blank, copy n1
    if(n2.value == undefined){
      n2.value = n1.value
    }
    // now displaying the result
    displayingRes = true
    // evaluate based on operation
    switch(currOp){
      case '+':
        n1.value += n2.value
        break
      case '-':
        n1.value -= n2.value
        break
      case 'x':
        n1.value *= n2.value
        break
      case '÷':
        n1.value /= n2.value
        break
      case '%':
        n1.value %= n2.value
        break
      case '&':
        n1.value &= n2.value
        break
      case '|':
        n1.value |= n2.value
        break
      case '^':
        n1.value ^= n2.value
        break
      default:
        // n1.value = n1.value
    }
  }
  // keep n2 and currOp, clear n1
  function clear() {
    n1.value = 0
  }
  // reset n1, n2 and op
  function reset() {
    n1.value = 0
    n2.value = undefined
    currOp = undefined
  }
  // toggle base (decimal or binary)
  function changeBase() {
    base.value = (base.value == 2) ? 10 : 2
  }
</script>

<!-- HTML (body content) goes here -->
<template>
  <div id="calc">
    <h2>Calculator :)</h2>
    <!-- value display -->
    <div class="display">{{nDisplayed}}</div>
    <!-- digit buttons -->
    <div id="digit-wrapper">
      <button v-for="i in digits" @click="addDigit(i)">{{i}}</button>
    </div>
    <!-- operational buttons -->
    <div id="op-wrapper">
      <button v-for="op in operations" @click="chooseOp(op)">{{op}}</button>
      <button @click="evaluate">=</button>
    </div>
    <!-- functional buttons -->
    <div id="func-wrapper">
      <button @click="clear">Clear</button>
      <button @click="reset">Reset</button>
    </div>
    <!-- special buttons -->
    <div id="special-wrapper">
      <button @click="changeBase">To {{baseStr}}</button>
    </div>
  </div>
</template>

<style>
  * {
    font-family: monospace;
    font-size: 64px;
    margin: 0;
  }
  body {
    height: 100vh;

    display: flex;
    align-items: center;
    justify-content: center;

    background: gainsboro;
  }

  button {
    width: max-content;
    height: min-content;
    font-size: 48px;
  }

  .display {
    text-align: right;

    height: 72px;
    min-width: 388px;
    width: fit-content;
    background: white;
    border: 2px solid black;
    border-radius: 15px;
  }
</style>