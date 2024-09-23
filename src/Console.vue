<script setup>
  import { ref, watch } from 'vue'

  const props = defineProps({
    num1: Number,
    op: String,
    num2: Number,
    numOps: Number
  })

  const history = ref([])

  // when opCount changes, either clear or add to history
  /*
  watch(numOps, (newNumOps) => {
    // clear history if reset
    if(newNumOps == 0){
      history.value = []
      return
    }
    // else, add current state
    history.value.push({
      thisNum1: num1.value,
      thisOp: op.value,
      thisNum2: num2.value
    })
  })
  */

</script>

<template>
  <div id="console-wrapper">
    <h1>Console</h1>
    <div id="console">
      <ul>
        <!--<li v-for="state in history">{{state.thisNum1}} {{state.thisOp}} {{state.thisNum2}} </li>
      -->
      </ul>
    </div>
  </div>
</template>

<style scoped>
  * {
    margin: 0;
    padding: 0;
  }

  #console-wrapper {
    margin-top: 20px;
  }

  h1 {
    font-family: sans-serif;
    color: darkgray;
    font-size: 32px;
  }

  #console {
    border: 2px solid lightgray;
    border-radius: 5px;
    background: black;
  }

  li {
    color: #00ff00;
    list-style-type: none;
    font-size: 16px;
    margin: 10px;
  }

  li::before {
    content: "> "
  }

</style>