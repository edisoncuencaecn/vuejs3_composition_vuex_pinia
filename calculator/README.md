# calculator

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
components:
Listen computed data via props from parent component App.vue view parent components
Also passing computed data via emit to parent component App.vue

'router' Directory to setup index.js which contains the project's routes.
route level code-splitting

'stores' Directory To setup setup/composition or options stores.js via pinia or vue in order to create a Global state managment to share state, actions & getters amoung parent or child components

index.html, is where the UI is rendered

main.js, is the entry point for Vue.js

App.vue is main view, main data computed and Parent component passing its computed data to respective child /src/components as follows:

     Methods() are executed every time that the method is called via 'v-on', accept parameters.
     Computed() are only executed when its dependencies values are changed via v-on, otherwise uses
     its cache. Do not accept parameters.
     Watchers() allow to watch any data or computed property change and execute
     some of the code in response to those changes in their values 'v-model'. They are triggered
     when an event has occurred, usefull for asynchronous process like fetch a database.
     Watchers do not execute when objects elements change their values unless deep:true.
  