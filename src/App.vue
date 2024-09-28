<!-- JS goes here -->
<script setup>
  import { ref, computed } from 'vue'
  import Console from './Console.vue'

  function fact(n){
    if(!Number.isInteger(n) || n < 0) return NaN
    // set upper limit
    if(n >= 200) return Infinity
    let prod = 1
    while(n > 0){
      prod *= n--
    }
    return prod
  }

  // first operand
  const n1 = ref(0)
  // current operation
  const currOp = ref(undefined)
  // second operand
  const n2 = ref(undefined)
  // all binary operations
  const operations = ['+', '-', '*', '/', '%', '&', '|', '^', '**']
  // all unary operations
  const unaryOperations = ['±', '1/x', 'x!', 'sq', 'sqrt']
  // constants
  const constants = ['e', 'π', 'γ']

  // whether entering n2 or not
  const second = ref(false)
  // whether displaying result of a computation
  var displayingRes = false
  // multiplier for new decimal digits
  const decMultiplier = ref(undefined)

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
  /* TODO:
    - round number to fit in display
    - display trailing 0s in decimal mode
  */
  const nDisplayed = computed(() => {
    let displayStr = 0
    // show n2 if it is required and exists
    if(second.value && n2.value != undefined) {
      displayStr = n2.value
    } else {
      displayStr = n1.value
    }
    // show in correct base (convert to string)
    displayStr = displayStr.toString(base.value)
    // show decimal point if just placed
    if(decMultiplier.value == (1/base.value)){
      displayStr += '.'
    }
    return displayStr
  })

  // number of operations performed
  const opCount = ref(0)
  // needed to send to child
  const oldN1 = ref(undefined)

  // try to add digit to num
  function addDigit(i) {
    if(second.value){ // add to n2 if n2 required
      // overwrite computational result
      if(displayingRes){
        n2.value = i
        displayingRes = false
      } else if(decMultiplier.value != undefined){ // handle decimal mode
        // n2 should not be undefined here
        n2.value += i * decMultiplier.value
        decMultiplier.value /= base.value
      } else { // regular mode
        n2.value = (n2.value == undefined) ? i : base.value * n2.value + i
      }
    } else { // add to n1
      // if displaying computation result, overwrite
      if(displayingRes){
        n1.value = i
        displayingRes = false
      } else if(decMultiplier.value < 1){ // handle decimal
        n1.value += i * decMultiplier.value
        decMultiplier.value /= base.value
      } else { // regular
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
    currOp.value = op
    // now we need n2
    second.value = true
    // set n2 value undefined
    n2.value = undefined
    // reset decimal place
    decMultiplier.value = undefined
  }
  // choose (and eval) unary operation
  function chooseUnaryOp(op){
    // displaying a result
    displayingRes = true
    // reset decimal place
    decMultiplier.value = undefined
    // perform on n2 if conditions met
    if(second.value && n2.value != undefined){
      n2.value = evaluateUnary(op, n2.value)
    } else { // perform on n1
      n1.value = evaluateUnary(op, n1.value)
    }
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
    // reset decimal place
    decMultiplier.value = undefined
    // save old value of n1
    oldN1.value = n1.value
    // evaluate based on operation
    switch(currOp.value){
      case '+':
        n1.value += n2.value
        break
      case '-':
        n1.value -= n2.value
        break
      case '*':
        n1.value *= n2.value
        break
      case '/':
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
      case '**':
        n1.value **= n2.value
        break
      default: // currOp undefined
        return
    }
    // increment opCount
    opCount.value++
  }
  // return new value of unary operation
  function evaluateUnary(op, num){
    // now displaying a result
    displayingRes = true
    // reset decimal place
    decMultiplier.value = undefined
    // evaluate based on operation
    switch(op){
      case '±':
        return -num
      case '1/x':
        return 1/num
      case 'sqrt':
        return num **= 0.5
      case 'sq':
        return num **= 2
      case 'x!':
        return fact(num)
      default:
        return num
    }
  }
  // keep n2 and currOp, clear n1
  function clear() {
    n1.value = 0
    // not displaying a result
    displayingRes = false
    // reset decimal place
    decMultiplier.value = undefined
  }
  // reset n1, n2 and op
  function reset() {
    n1.value = 0
    n2.value = undefined
    currOp.value = undefined
    // clear history
    opCount.value = 0
    // not displaying a result
    displayingRes = false
    // reset decimal place
    decMultiplier.value = undefined
  }
  // place decimal point
  function placeDecimal() {
    // not displaying a result
    displayingRes = false
    // return if decimal mult already initialized
    if(decMultiplier.value != undefined) return
    decMultiplier.value = 0.1
    // make n2 0 if needed and undefined
    if(second.value && n2.value == undefined){
      n2.value = 0
    }
  }
  // toggle base (decimal or binary)
  function changeBase() {
    base.value = (base.value == 2) ? 10 : 2
  }
  // load a constant into either n1 or n2
  function loadConstant(c){
    displayingRes = true
    decMultiplier.value = undefined
    // load constant value
    switch(c){
      case 'e':
        c = Math.E
        break
      case 'π':
        c = Math.PI
        break
      case 'γ':
        c = 0.577215664901533
        break
      default:
        c = 0
    }
    if(second.value){
      n2.value = c
    } else {
      n1.value = c
    }
  }
</script>

<!-- HTML (body content) goes here -->
<template>
  <div id="calc">
    <h2>Vue Calculator :)</h2>
    <!-- value display -->
    <div class="display">{{nDisplayed}}</div>
    <!-- digit buttons -->
    <div id="digit-wrapper" class="button-wrapper">
      <button @click="placeDecimal">.</button>
      <button v-for="i in digits" @click="addDigit(i)">{{i}}</button>
    </div>
    <!-- operational buttons -->
    <div id="op-wrapper" class="button-wrapper">
      <button v-for="op in operations" @click="chooseOp(op)">{{op}}</button>
    </div>
    <!-- unary operational buttons -->
    <div id="unary-wrapper" class="button-wrapper">
      <button v-for="op in unaryOperations" @click="chooseUnaryOp(op)">{{op}}</button>
    </div>
    <!-- functional buttons -->
    <div id="func-wrapper" class="button-wrapper">
      <button @click="evaluate">=</button>
      <button @click="clear">Clear</button>
      <button @click="reset">Reset</button>
    </div>
    <!-- special buttons -->
    <div id="special-wrapper" class="button-wrapper">
      <button v-for="c in constants" @click="loadConstant(c)">{{c}}</button>
      <button @click="changeBase">To {{baseStr}}</button>
    </div>
  </div>
  <!-- use props to pass internal state to child-->
  <Console :num1="oldN1" :op="currOp" :num2="n2" :res="n1" :numOps="opCount" />
</template>

<style>
  * {
    font-family: monospace;
    font-size: 64px;
    margin: 0;
  }
  h2 {
    text-align: center;
    margin-top: 20px;
  }
  body {
    height: 100vh;

    display: flex;
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
    min-width: 6px;
    background: white;
    border: 2px solid black;
    border-radius: 15px;
    margin-top: 8px;
    padding-right: 4px;
  }

  .button-wrapper {
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
    margin-top: 8px;
  }
</style>