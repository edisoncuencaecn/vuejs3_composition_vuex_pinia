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
   let resultPrevious = ref('0.00');
   let keyPrevious = ref('0');
   let numDigits = ref(0); 
   // starts keyboard
   const operations = ref(['=','%','Delete','Enter']); 
   const allowedKeys = ref(['0','1','2','3','4','5','6','7','8','9','+','-','/','*','=','%','Delete','Enter','.']); 
   const operators = ref(['+','-','/','*','=','%','.']); 
   const mathOperators = ref(['+','-','/','*','%']); 
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
        if (result.value === 'error' || result.value === 'Infinity' || result.value === '-Infinity'){
         result.value ='';
        };
       result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value?.length ?? 0) : `${result.value}`; // Optional chaining data?.length ?? 0 (ES2020)
       result.value =  result.value[0] === "." ? result.value.charAt(1,result.value?.length ?? 0) : `${result.value}`;
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
        // if first input is '0' and the second is '.' then accepts it because it's a decimal number to be input
        result.value =  !mathOperatorExist() && keyPrevious.value == "0" && event.key === "." ?  `${" 0."}` : `${result.value}`; // adding condition && there's not already a operator
        resultPrevious.value = result.value ? result.value : `${result.value}`;
        result.value = `${result.value}${event.key}`; 
        keyPrevious.value = event.key;
        // removes first character when it's an operator  
        result.value =  isKeyForOperators(result.value[0]) && (result.value?.length ?? 0) === 1 ? "0.00" : result.value;
        // removes second operator if it's input in a row
        result.value =  isKeyForOperators(result.value[(result.value?.length ?? 0)-1]) && isKeyForOperators(result.value[(result.value?.length ?? 0)-2]) ? result.value.slice(0,((result.value?.length ?? 0)-1)) : result.value; //here
        // removes second operator period if it's input 
        result.value =  isKeyForOperators(result.value[(result.value?.length ?? 0)-1]) && isPeriodKeyReplicated('.') ? result.value.slice(0,((result.value?.length ?? 0)-1)) : result.value; //here
        numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
        // To remove 'Shift' key from keyboardEvent 'Shift =' & 'Shift *'
        result.value =  result.value.indexOf(shift) !== -1 ? result.value.slice(0, (result.value.indexOf(shift)), "+") : result.value;
        if (numDigits.value > 21){ 
          result.value = resultPrevious.value; // just won't add the new digit nor operator    
        };
      }
    };
    
    const isKeyForOperation = (key) => {
       return operations.value.includes(key);
    };

    const isKeyForOperators = (key) => {
       return operators.value.includes(key);
    };

    // find all the duplicated characters in result.value but returns true if one of those is a period '.'
    const isPeriodKeyReplicated = (key) => {
      const duplicates = new Set();
      const seenCharacters = new Set();
      for (let i = 0; i < result.value.length; i++) {
        const char = result.value[i];
        if (seenCharacters.has(char)) {
          duplicates.add(char);
        }  else {
          seenCharacters.add(char);
        }
        if (mathOperators.value.includes(char)) {
          // Reset seencharacters if a matOperator is encountered in between them
          seenCharacters.clear();
        }
      }
      return (duplicates.size > 0 && duplicates.has(key));
    };

    const mathOperatorExist = () => {
      for (let i = 0; i < result.value.length; i++) {
        const char = result.value[i];
        if (mathOperators.value.includes(char)) {
         return true;
        }
      }
      return false;
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
     if (result.value === 'error' || result.value === 'Infinity' || result.value === '-Infinity'){
       result.value ='';
     };
     result.value =  result.value[0] === "0" ? result.value.charAt(1,result.value?.length ?? 0) : `${result.value}`;
     result.value =  result.value[0] === "." ? result.value.charAt(1,result.value?.length ?? 0) : `${result.value}`;
     // if first input is '0' and the second is '.' then accepts it because it's a decimal number to be input 
     result.value =  !mathOperatorExist() && keyPrevious.value == "0" && key === "." ?  `${" 0."}` : `${result.value}`; // adding condition && there's not already a operator
     resultPrevious.value = result.value ? result.value : `${result.value}`;
     result.value = `${result.value}${key}`;  
     keyPrevious.value = key;
     // removes first character when it's an operator  
     result.value =  isKeyForOperators(result.value[0]) && (result.value?.length ?? 0) === 1 ? "0.00" : result.value;
     // removes second operator if it's input in a row
     result.value =  isKeyForOperators(result.value[(result.value?.length ?? 0)-1]) && isKeyForOperators(result.value[(result.value?.length ?? 0)-2]) ? result.value.slice(0,((result.value?.length ?? 0)-1)) : result.value; //here
     // removes second operator period if it's input 
     result.value =  isKeyForOperators(result.value[(result.value?.length ?? 0)-1]) && isPeriodKeyReplicated('.') ? result.value.slice(0,((result.value?.length ?? 0)-1)) : result.value; //here

     numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
     if (numDigits.value > 21){ 
       result.value = resultPrevious.value; // just won't add the new digit nor operator    
     };
    };
   
   const signDisplay = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     if (result.value === 'error' || result.value === 'Infinity' || result.value === '-Infinity'){
         return result.value ='0.00';
     };
     resultPrevious.value = result.value ? result.value : `${result.value}`;
     if  (isKeyForOperators(result.value[(result.value?.length ?? 0)-1])){
        // if last character is an operator, don't do anything because the formula is pending
        result.value = resultPrevious.value; // just won't add the new digit nor operator  
        return result.value;
     }
     // removes '-' if it's already there, otherwise add it
     try {
       resultPrevious.value = result.value ? result.value : `${result.value}`;
       result.value = `${eval(result.value)}`; // evaluates if +/- is input in a pending formula
       result.value =  result.value <= 0 ? `${result.value *(-1)}` : `-${result.value}`;
       numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
       if (numDigits.value > 21){ 
        result.value = resultPrevious.value; // just won't add the new digit nor operator    
       };
     } catch (error) {
       result.value = "error";
     }
   };

   const clearDisplay = () => {
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     result.value = '0.00';
     resultPrevious.value = '0.00';
     numDigits.value = 0;
     keyPrevious.value = '0';
   };


   const percentToDisplay = () => {
     resultPrevious.value = result.value ? result.value : `${result.value}`;
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
   
     try {
       // removes first character when it's an operator  
       if (result.value > 0) {
         console.log((result.value));
         result.value = `${result.value}/100`;  
         numDigits.value = result.value?.length ?? 0; // Optional chaining data?.length ?? 0 (ES2020)
         if (numDigits.value > 21){ 
           result.value = resultPrevious.value; // just won't add the new digit nor operator 
         } 
      }
     } catch {
       result.value = "error";
     }
   };
   
  const calculate = () => {
    if (isKeyboard){
      setFocus(); // sets automatic keyboard keys focus
    };
    resultPrevious.value = result.value ? result.value : `${result.value}`;
    if  (isKeyForOperators(result.value[(result.value?.length ?? 0)-1])){
        // if last character is an operator, don't do anything because the formula is pending
    }else{
       try {
        //result.value = `${eval(result.value)}`;
        result.value = `${parseFloat(eval(result.value).toFixed(11))}`;
         if (numDigits > 21){
           result.value = resultPrevious.value; // just won't add the new digit nor operator 
         } 
       } catch (error) {
         result.value = "error";
       }
    };
   };

</script>

<style lang="scss" scoped>

</style>