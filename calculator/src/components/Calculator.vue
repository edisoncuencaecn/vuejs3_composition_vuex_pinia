<!-- Listen computed data via props from parent component App.vue view parent components
Also passing computed data via emit to parent component App.vue-->
<template>
    <div id="calculator">
    <Display v-bind:result="result" />
    <div v-bind:class="{ 'add-keyboard': isKeyboard, 'remove-keyboard': !isKeyboard}">
      <input v-on:keyup="handleKeyUp" autofocus ref="autoInput"></input>
    </div>
    <div id="keys">
        <button v-on:click="clearDisplay()" class="operator-button">AC</button>
        <button v-on:click="signDisplay()" class="operator-button">+/-</button>
        <button v-on:click="percentToDisplay('%')" class="operator-button">%</button>
        <button v-on:click="toDisplay('/')" class="operator-button">/</button>
        <button v-on:click="toDisplay('7')">7</button>
      
        <button v-on:click="toDisplay('8')">8</button>
        <button v-on:click="toDisplay('9')">9</button>
        <button v-on:click="toDisplay('*')" class="operator-button">x</button>
       
        <button  v-on:click="toDisplay('4')">4</button>
        <button  v-on:click="toDisplay('5')">5</button>
        <button  v-on:click="toDisplay('6')">6</button>
        <button  v-on:click="toDisplay('-')" class="operator-button">-</button>
        
        <button  v-on:click="toDisplay('1')">1</button>
        <button  v-on:click="toDisplay('2')">2</button>
        <button  v-on:click="toDisplay('3')">3</button>
        <button  v-on:click="toDisplay('+')" class="operator-button">+</button>
        
        <!--keyboard-->
        <button v-on:click="switchToKeyboard()">
            <span>{{ isKeyboard ? 'K' : 'M'}}</span>
        </button>

        <button v-on:click="toDisplay('0')">0</button>
        <button v-on:click="toDisplay('.')">.</button>
        <button v-on:click="calculate()" class="operator-button">=</button>
        
    </div>
    </div>
</template>

<script setup>
   import Display from './Display.vue'
   import {ref, nextTick  } from 'vue'
   // starts, setting automatic focus to keyboard keys listening on top of mouse ones
   const autoInput = ref(null); // input ref
   const setFocus = () => { 
          // Ensure the DOM input is rendered before attempting to focus
          nextTick(() => {
            if (autoInput.value) {
              autoInput.value.focus();
            }
          });
    };
    // set focus ends

   let result = ref('0.00');
   let numDigits = ref(0); // max number validation
   // starts keyboard
   const operations = ref(['=','%','Delete','Enter']); 
   const allowedKeys = ref(['0','1','2','3','4','5','6','7','8','9','+','-','/','*','=','%','Delete','Enter']); 
   const shift = 'Shift'; 
   let isKeyboard = ref(false); 

   const switchToKeyboard = () => {
     isKeyboard.value = !isKeyboard.value;
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
   };

   const handleKeyUp = (event) => {
     event.preventDefault();
     if (!hasAllowedKeys(event.key)){return};
       result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value?.length ?? 0) : result.value; // Optional chaining data?.length ?? 0 (ES2020)
       result.value =  result.value[0] === "." ? result.value.charAt(1,result.value?.length ?? 0) : result.value;
       // if keyboard key is a n operations element, it means it won't be added to the string nor diplay it
       if (isKeyForOperation(event.key)){
         switch (event.key) {
          case operations.value[0]:
            calculate();
            break;
          case operations.value[1]:
            percentToDisplay();
            break;
          case operations.value[2]:
            clearDisplay();
            break;
          case operations.value[3]:
            calculate();
            break;
          default:
            break
        }
      }else{
        result.value = `${result.value}${event.key}`; 
        numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
        // To remove 'Shift' key from keyboardEvent 'Shift =' & 'Shift *'
        result.value =  result.value.indexOf(shift) !== -1 ? result.value.slice(0, (result.value.indexOf(shift)), "+") : result.value;
        if ((result.value > 10000000000)||(numDigits.value > 11)){ 
          result.value = "error";      
        };
      }
    };
    
    const isKeyForOperation = (key) => {
       return operations.value.includes(key);
    };
    const hasAllowedKeys = (key) => {
       return allowedKeys.value.includes(key);
    };
    // ends keyboard

   //`` backticks embed expressions (variables, function calls, arithmetic operations, etc.) directly within a string.
   // removes left first '0' or '.' and embed pressed key to main result string
   const toDisplay = (key) => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value?.length ?? 0) : result.value;
     result.value =  result.value[0] === "." ? result.value.charAt(1,result.value?.length ?? 0) : result.value;
     result.value = `${result.value}${key}`;  
     numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
     if ((result.value > 10000000000)||(numDigits.value > 11)){ 
       result.value = "error";      
     };
    };
   
   const signDisplay = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     // removes '-' if it's already there, otherwise add it
     try {
       result.value =  result.value <= 0 ? result.value *(-1) : `-${result.value}`;
       if ((result.value > 10000000000)||(numDigits.value > 11)){ 
         result.value = "error";      
       }else{
        numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
       };
     } catch (error) {
       result.value = "error";
    }
   };

   const clearDisplay = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     result.value = '0';
     numDigits.value = 0;
   };


   const percentToDisplay = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     try {
       result.value = `${result.value}/100`;   
     } catch {
       result.value = "error";
     }
   };
   
  const calculate = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
    try {
      result.value = eval(`${result.value}`); 
      if ((result.value > 10000000000) || (numDigits > 11)){
        result.value = "error";
      } 
    } catch (error) {
      result.value = "error";
    }
   };

</script>

<style lang="scss" scoped>

</style>