<!-- JS goes here -->
<script setup>
  import { ref } from 'vue'

  // current number value
  const num = ref(undefined)
  // old number (stored when operation chosen)
  var oldNum = undefined
  // all operations
  const operations = ['+', '-', 'x', '÷']
  // current operation
  var currOp = undefined

  // add digit to num
  function addDigit(i) {
    num.value = (num.value == undefined) ? i : num.value*10 + i
  }
  // choose operation
  function chooseOp(op){
    // set operation
    currOp = op
    // save num
    oldNum = num.value
    // clear num
    clear()
  }
  // evaluate oldNum `op` num
  function evaluate(){
    switch(currOp){
      case '+':
        num.value = oldNum + num.value
        break
      case '-':
        num.value = oldNum - num.value
        break
      case 'x':
        num.value = oldNum * num.value
      break
      case '÷':
        num.value = oldNum / num.value
        break
      default:
        num.value = undefined
    }
  }
  // reset num, keeping history (oldNum)
  function clear() {
    num.value = null
  }
  // reset num and history (oldNum)
  function reset() {
    num.value = oldNum = undefined
  }
</script>

<!-- HTML (body content) goes here -->
<template>
  <div id="calc">
    <h2>Calculator :)</h2>
    <!-- value display -->
    <div class="display">{{num}}</div>
    <!-- digit buttons -->
    <div id="digit-wrapper">
      <button v-for="i in Array(10).keys()" @click="addDigit(i)">{{i}}</button>
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
    <!-- operation buttons-->
    
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