# Read 「Vue3 Guide」

# Getting Started

## Introduction

### Trait

- JavaScript framework
- declarative and component-based

### Minimal Example

```javascript
import { createApp } from 'vue'

createApp({
	data() {
		return {
			count: 0
		}
	}
}).mount('#app')
```

```other
<div id="app> 
	<button @click="count++">
		Count is: {{ count }}
	</button>
</div>
```

The count increases every time the button is clicked.

The above example demonstrates the two core features of Vue:

- Declarative Rendering: we can use JavaScript state to describe HTML with the template syntax.
- Reactivity: tracking changes automatically and updating DOM efficiently.

### Single-File Components

- It also known as `*.vue` files, abbreviated as SFC.
- It encapsulates the component’s logic, template, and styles in single file.

Here’s the previous example, written in SFC format:

```other
<script>
export default {
	data() {
		return {
			count: 0
		}
	}
}
</script>

<template>
	<button @click="count++">
		Count is: {{ count }}
	</button>
</template>

<style scoped>
button {
	font-weight: bold;
}
</style>
```

### API styles

- Options API

```other
<script>
export default {
  // Properties returned from data() becomes reactive state
  // and will be exposed on `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event listeners in templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">count is: {{ count }}</button>
</template>
```

`The data` , `methods` , and `mounted` are `this` ‘s Properties, and `this` points to the component instance.

- Composition AIP

```other
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

The `setup` attribute is a hint that makes Vue perform compile-time transforms that allow us to use Composition API. For example, imports and top-level variables / functions declared in script are directly usable in the template.

## Quick Start

### With Build Tools

> The official Vue build setup is based on Vite, a front end build tool that is modern, lightweight and extremely fast.

#### StackBlitz: online Vite.

StackBlitz runs the Vite-based build setup directly in the browser.

#### Install vite: command line:  `npm init vue@latest` .

The above command line will install and execute `create-vue` which is the official Vue project scaffolding tool. and it have a number of optional features.

- Add TypeScript?
- Add JSX Support?
- Add Vue Router?
- Add Pinia?
- Add Vitest?
- Add Cypress?
- Add ESLint?
- Add Prettier?

Run `npm install` and `npm run dev` to start the dev server.

Run `npm run build` to ship your app to production.

### Without Build Tools

copy simply the following code into an HTML file:

```other
<script src="https://unpkg.com/vue@3"></script>

<div id="app">{{ message }}</div>

<script>
  Vue.createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

The above example exposed the global `Vue` variable.

But if use Vue over native ES modules, following HTML instead:

```other
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

The above codes works by the `<script type=“importmap”>` block. If your browser does not support import maps yet, you can polyfill it with `es-module-shims` .

Then run `npx serve` form the command line in the directory where your HTML file is.

# Essentials

## Creating a Vue Application

`createApp` function is used to create an application instance.

```other
import { createApp } from 'vue'

const app = createApp({
	/* root component options */
})
```

`createApp` function’s parameter is  an component object.

Every app requires a root component that can contain other components as its children.

App’s m`ount()` method is used to accept a DOM element or a selector string, and the method’s return value is the root component instance.

```other
<div id='app'></div>
```

```other
app.mount('#app')
```

App root component’s content will be rendered inside the element.

App instance exposes a `.config` object that allows us to configure a few app-level options, for example an app-level erro handler:

```other
app.config.errorHandler = (err) => {
	/* handle error */
}
```

App instance also provides a few methods for registering app-scoped assets, for example, registering a component:

```other
app.component('TodoDeleteButton', TodoDeleteButton)
```

`TodoDeleteButton` is available anywhere in the app.

More APIs is in API reference.


