<script setup>

</script>

<template>
  <div id="console-wrapper">
    <h1>Console</h1>
    <div id="console">
      <ul>
        <li>5 + 2 = 7</li>
        <li>7 + 2 = 9</li>
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