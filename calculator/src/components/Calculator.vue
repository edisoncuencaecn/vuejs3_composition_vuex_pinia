<template>
    <div id="calculator">
    <div v-bind:result="result" id="display">{{ 0.00 || result }}</div>
    <div id="keys">
        <button v-on:click="toDisplay('ac')" type="submit" value="ac" class="operator-key">AC</button>
        <button type="submit" value="+/-" class="operator-key">+/-</button>
        <button type="submit" value="%" class="operator-key">%</button>
        <button type="submit" value="/" class="operator-key">/</button>
        <button v-on:click="toDisplay('7')" type="submit" value="7">7</button>
        <button v-on:click="toDisplay('8')" type="submit" value="8">8</button>
        <button v-on:click="toDisplay('9')" type="submit" value="9">9</button>
        <button type="submit" value="x" class="operator-key">x</button>
       
        <button v-on:click="toDisplay('4')" type="submit" value="4">4</button>
        <button v-on:click="toDisplay('5')" type="submit" value="5">5</button>
        <button v-on:click="toDisplay('6')" type="submit" value="6">6</button>
        <button type="submit" value="-" class="operator-key">-</button>
        
        <button v-on:click="toDisplay('1')" type="submit" value="1">1</button>
        <button v-on:click="toDisplay('2')" type="submit" value="3">2</button>
        <button v-on:click="toDisplay('3')" type="submit" value="3">3</button>
        <button type="submit" value="+" class="operator-key">+</button>

        <button type="submit" value="?">?</button>
        <button v-on:click="toDisplay('0.00')" type="submit" value="0">0</button>
        <button v-on:click="toDisplay('.')" type="submit" value=".">.</button>
        <button type="submit" value="=" class="operator-key">=</button>
    </div>
    </div>
</template>

<script setup>
   import {ref} from 'vue';
   const result = ref('');
   const toDisplay = (key) => {
      result.value = result.value + key;
      return result.value;
   };

</script>

<style lang="scss" scoped>

</style>