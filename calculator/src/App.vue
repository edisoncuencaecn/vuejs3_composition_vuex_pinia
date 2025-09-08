/* Main view, main data computed and Parent component passing its computed data to respective child /src/components as follows:
     Methods() are executed every time that the method is called via 'v-on', accept parameters.
     Computed() are only executed when its dependencies values are changed via v-on, otherwise uses
     its cache. Do not accept parameters.
     Watchers() allow to watch any data or computed property change and execute
     some of the code in response to those changes in their values 'v-model'. They are triggered
     when an event has occurred, usefull for asynchronous process like fetch a database.
     Watchers do not execute when objects elements change their values unless deep:true.
*/
<script setup>
   import Calculator from './components/Calculator.vue';
</script>

<template>
  <div>
     <Calculator />
  </div>
</template>

<style scoped></style>
