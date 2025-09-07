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
   let numDigits = ref(0); // max numver validation
   // starts keyboard
   const operations = ref(['=','%','Delete']); 
   const allowedKeys = ref(['0','1','2','3','4','5','6','7','8','9','+','-','/','*','=','%','Delete']); 
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
     numDigits.value++;
     result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value.length) : result.value;
     result.value =  result.value[0] === "." ? result.value.charAt(1,result.value.length) : result.value;
     result.value =  result.value[0] === "-" ? result.value.charAt(1,result.value.length) : result.value;
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
         default:
            break
        }
     }else{
       result.value = `${result.value}${event.key}`; 
       // To remove 'Shift' key from keyboardEvent 'Shift =' & 'Shift *'
       result.value =  result.value.indexOf(shift) !== -1 ? result.value.slice(0, (result.value.indexOf(shift)), "+") : result.value;
       if ((result.value.length > 10000000000)||(numDigits.value>11)){ 
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
     numDigits.value++;
     result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value.length) : result.value;
     result.value =  result.value[0] === "." ? result.value.charAt(1,result.value.length) : result.value;
     result.value =  result.value[0] === "-" ? result.value.charAt(1,result.value.length) : result.value;
     result.value = `${result.value}${key}`;  
     if ((result.value.length > 10000000000)||(numDigits.value > 11)){ 
       result.value = "error";      
     };
    };
   
   const signDisplay = () => {
     // removes '-' if it's already there, otherwise add it
     numDigits.value++;
     try {
       result.value =  result.value[0] === "-" ? `${result.value.slice(1,result.value.length)}` : `-${result.value}`;
       if ((result.value.length > 10000000000)||(numDigits.value > 11)){ 
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
       //calculate();
     } catch {
       result.value = "error";
     }
   };
   
  const calculate = () => {
    try {
      result.value = eval(`${result.value}`); 
      if ((result.value.length > 10000000000) || (numDigits > 11)){
        result.value = "error";
      } 
    } catch (error) {
      result.value = "error";
    }
   };

</script>

<style lang="scss" scoped>

</style>