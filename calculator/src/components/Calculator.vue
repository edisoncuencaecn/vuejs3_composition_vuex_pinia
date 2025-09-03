<template>
    <div id="calculator">
    <div v-bind:result="result" id="display">{{ 0.00 || result }}</div>
    <div id="keys">
        <button v-on:click="clearDisplay()" class="operator-button">AC</button>
        <button v-on:click="signDisplay()" class="operator-button">+/-</button>
        <button v-on:click="percentToDisplay('%')" class="operator-button">%</button>
        <button v-on:click="toDisplay('/')" class="operator-button">/</button>
        <button v-on:click="toDisplay('7')">7</button>
        <button v-on:click="toDisplay('8')">8</button>
        <button v-on:click="toDisplay('9')">9</button>
        <button v-on:click="toDisplay('*')" class="operator-button">x</button>
       
        <button v-on:click="toDisplay('4')">4</button>
        <button v-on:click="toDisplay('5')">5</button>
        <button v-on:click="toDisplay('6')">6</button>
        <button v-on:click="toDisplay('-')" class="operator-button">-</button>
        
        <button v-on:click="toDisplay('1')">1</button>
        <button v-on:click="toDisplay('2')">2</button>
        <button v-on:click="toDisplay('3')">3</button>
        <button v-on:click="toDisplay('+')" class="operator-button">+</button>

        <button v-on:click="help()">?</button>
        <button v-on:click="toDisplay('0')">0</button>
        <button v-on:click="toDisplay('.')">.</button>
        <button v-on:click="calculate()" class="operator-button">=</button>
    </div>
    </div>
</template>

<script setup>
   import {ref} from 'vue';
   const result = ref('0.00');

   const toDisplay = (key) => {
      result.value =  result.value.charAt(0) === "0" ? result.value.charAt(1,result.value.length) : result.value;
      result.value =  result.value.charAt(0) === "." ? result.value.charAt(1,result.value.length) : result.value;
      result.value = `${result.value}${key}`; 
   };
   
   const signDisplay = () => {
      console.log(result.value);
      try {
       result.value =  result.value.charAt(0) === "-" ? `${result.value.slice(1,result.value.length)}` : `-${result.value}`;
      } catch (error) {
       result.value = "error";
      }
   };

   const clearDisplay = () => {
      result.value = '0'
   };

   const help = () => {
    alert('type percentNumber followed by "%" then "x" followed by the number to get % from, finally "="');
   }

   const percentToDisplay = () => {
      try {
        result.value = `${result.value}/100`;
      } catch {
        result.value = "error";
      }
   };
   
  const calculate = () => {
      try {
        result.value = eval(`${result.value}`); //embed expressions (variables, function calls, arithmetic operations, etc.) directly within a string.
      } catch (error) {
        result.value = "error";
      }
   };

</script>

<style lang="scss" scoped>

</style>