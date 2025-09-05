<template>
    <div id="calculator">
    <Display v-bind:result="result" />
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
   import Display from './Display.vue'
   import {ref} from 'vue';
   let result = ref('0.00');
   let numDigits = ref(0);
   
   //`` backticks embed expressions (variables, function calls, arithmetic operations, etc.) directly within a string.
   // removes left first '0' or '.' and embed pressed key to main result string
   const toDisplay = (key) => {
     numDigits.value++;
     result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value.length) : result.value;
     result.value =  result.value[0] === "." ? result.value.charAt(1,result.value.length) : result.value;
     result.value = `${result.value}${key}`;  
     if ((result.value.length > 10)||(numDigits.value>10)){ 
       result.value = "error";      
     };
    };
   
   const signDisplay = () => {
     // removes '-' if it's already there, otherwise add it
     numDigits.value++;
     try {
       result.value =  result.value[0] === "-" ? `${result.value.slice(1,result.value.length)}` : `-${result.value}`;
       if ((result.value.length > 10)||(numDigits.value>10)){ 
         result.value = "error";      
       };
     } catch (error) {
       result.value = "error";
    }
   };

   const clearDisplay = () => {
     result.value = '0';
     numDigits.value = 0;
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
      result.value = eval(`${result.value}`); 
      if ((result.value> 1000000000) || (result.value.length > 10)){
        result.value = "error";
      } 
    } catch (error) {
      result.value = "error";
    }
   };

</script>

<style lang="scss" scoped>

</style>