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
   let resultEvalLength = ref(0);
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

    const getResutLength = () => {
      console.log('length:',result.value?.length ?? 0)
       return result.value?.length ?? 0
    }

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
       result.value =  result.value[0] === "0" ? result.value.charAt(1,getResutLength()) : `${result.value}`; // Optional chaining data?.length ?? 0 (ES2020)
       result.value =  result.value[0] === "." ? result.value.charAt(1,getResutLength()) : `${result.value}`;
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
        //resultPrevious.value = result.value ? result.value : `${result.value}`;
        result.value = `${result.value}${event.key}`; 
        keyPrevious.value = event.key;
        if (resultEvalLength.value > 1){ // its already evalatuaded then result.value.length > 0, do not check: BIG ISSUE with decimals (getResutLength()) won't work 0.05 says length=1
          result.value = `${resultPrevious.value}${event.key}`; // issue removed the '-'
          resultPrevious.value = result.value ? result.value : `${result.value}`;
        } else {
          // removes first character when it's an operator  
          result.value =  isKeyForOperators(result.value[0]) && (getResutLength()) === 1 ? "0.00" : `${result.value}`;//result.value;
        }
        // removes second operator if it's input in a row
        result.value =  isKeyForOperators(result.value[(getResutLength())-1]) && isKeyForOperators(result.value[(getResutLength())-2]) ? result.value.slice(0,((getResutLength())-1)) : `${result.value}`;//result.value; //here
        // removes second operator period if it's input 
        result.value =  isKeyForOperators(result.value[(getResutLength())-1]) && isPeriodKeyReplicated('.') ? result.value.slice(0,((getResutLength())-1)) : `${result.value}`;//result.value; //here
        numDigits.value = getResutLength(); // Optional chaining data?.length ?? 0 (ES2020)
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
    // vpi.toPrecision(5)

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
     result.value =  result.value[0] === "0" ? result.value.charAt(1,getResutLength()) : `${result.value}`;
     result.value =  result.value[0] === "." ? result.value.charAt(1,getResutLength()) : `${result.value}`;
     // if first input is '0' and the second is '.' then accepts it because it's a decimal number to be input 
     result.value =  !mathOperatorExist() && keyPrevious.value == "0" && key === "." ?  `${" 0."}` : `${result.value}`; // adding condition && there's not already a operator
     //resultPrevious.value = result.value ? result.value : `${result.value}`; // NaN
     result.value = `${result.value}${key}`;  // issue decimal number previously displayed + operator becaomes NaN
     keyPrevious.value = key;
     // removes first character when it's an operator  
     if (resultEvalLength.value > 1){ // its already evalatuaded then result.value.length > 0, do not check: BIG ISSUE with decimals (getResutLength()) won't work 0.05 says length=1
       //console.log('>length:',resultEvalLength.value,result.value[resultEvalLength.value], 'result.value:',result.value, Number(result.value).toPrecision(11), 'resultPrevious:',resultPrevious.value,'numberDigits:',numDigits.value);
       result.value = `${resultPrevious.value}${key}`; // issue removed the '-'
       resultPrevious.value = result.value ? result.value : `${result.value}`;
     } else {
        result.value =  isKeyForOperators(result.value[0]) && ((getResutLength()) === 1) ? "0.00" : `${result.value}`;//result.value;
     }
     // removes second operator if it's input in a row
     result.value =  isKeyForOperators(result.value[(getResutLength())-1]) && isKeyForOperators(result.value[(getResutLength())-2]) ? result.value.slice(0,((getResutLength())-1)) : `${result.value}`;//result.value; //here
     // removes second operator period if it's input 
     result.value =  isKeyForOperators(result.value[(getResutLength())-1]) && isPeriodKeyReplicated('.') ? result.value.slice(0,((getResutLength())-1)) : `${result.value}`;//result.value; //here

     numDigits.value = getResutLength(); // Optional chaining data?.length ?? 0 (ES2020)
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
     if  (isKeyForOperators(result.value[(getResutLength())-1])){
        // if last character is an operator, don't do anything because the formula is pending
        result.value = resultPrevious.value; // just won't add the new digit nor operator  
        return result.value;
     }
     // removes '-' if it's already there, otherwise add it
     try {
       resultPrevious.value = result.value ? result.value : `${result.value}`;
       result.value = `${eval(result.value)}`; // evaluates if +/- is input in a pending formula
       result.value =  result.value <= 0 ? `${result.value *(-1)}` : `-${result.value}`;
       numDigits.value = getResutLength(); // Optional chaining data?.length ?? 0 (ES2020)
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
     resultEvalLength.value = 0;
   };


   const percentToDisplay = () => {
     if (result.value === 'error' || result.value === 'Infinity' || result.value === '-Infinity'){
         return result.value ='0.00';
     };
     resultPrevious.value = result.value ? result.value : `${result.value}`;
     if (isKeyboard){
        setFocus(); // sets automatic keyboard keys focus
     };
     try {
       // removes first character when it's an operator  
       if (result.value > 0) {
         result.value = `${result.value}/100`;  
         //keyPrevious.value = '%';
         numDigits.value = getResutLength(); // Optional chaining data?.length ?? 0 (ES2020)
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
    if  (isKeyForOperators(result.value[(getResutLength())-1])){
        // if last character is an operator, don't do anything because the formula is pending
    }else{
       try {
        result.value = `${eval(result.value)}`;
        //result.value = `${parseFloat(eval(result.value).toFixed(11))}`;
         if (numDigits > 21){
           result.value = resultPrevious.value; // just won't add the new digit nor operator 
         } 
         resultEvalLength.value = getResutLength()
         resultPrevious.value = result.value ? result.value : `${result.value}`;
       } catch (error) {
         result.value = "error";
       }
    };
     //console.log('0.05'?.length,',length:',getResutLength(),result.value[`${result.value}`.length], `${result.value}`, Number(result.value).toPrecision(11), 'resultPrevious:',resultPrevious.value);
   };

</script>

<style lang="scss" scoped>

</style>