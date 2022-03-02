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

- It also knew as `*.vue` files, abbreviated as SFC.
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

App instance exposes a `.config` object that allows us to configure a few app-level options, for example an app-level error handler:

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

## Template Syntax

The HTML-based template syntax can be compiled into highly-optimized JavaScript code with the reactivity system.
you can also directly write render function instead of templates with optional JSX support.

* text interpolation: `{{ msg }}`
* raw HTML: `<span v-html="<span style='color:red'>This should be red</span>"></span>`
* attribute bindings: `<div v-bind:id="dynamicId"></div>` or `<div :id="dynamicId"></div>`
* boolean attributes: `<button :disabled="isButtonDisabled>Button</button>`

Each binding can only contain one single expression, so a statement will not work.

Template expression are sandbox and only have access to a restricted list of globals such as Math and Date.
Globals that not included in the list can be defined as additional globals for all VUe expressions by adding them to 
`app.config.globalProperties`.

Directives:

* v-html
* v-bind: v-bind can be condensed into a single character, `:`.
* v-for: v-on can be condensed into a single character, `@`.
* v-on
* v-slot

Dynamic Argument
* example:

```vue
<a v-bind:[attributeName]="url">...</a>
<a :[attributeName]="url">...</a>
<a v-on:[eventName]="doSomething">...</a>
<a @[eventName]="doSomething">...</a>
```
* Spaces and quotes are invalid in side HTML attribute names.
* naming attribute names with lowercase instead of uppercase.

modifiers

* v-on.prevent: call event.preventDefault().
...

Finally, here's the full directive syntax visualized:

`v-on:submit.precent="onSubmit"`

* `v-on:` is the name.
* `submit` is the argument.
* `.prevent` is the modifiers.
* `onSubmit` is the value.

## Reactivity Fundamentals

### Options API

* With Options AIP, the data option can be used to declare reactive state of a component.
* The data option should be a function that returns an object which will be wrapped in Vue reactivity system.
* When creating a new component instance Vue will call the option data function.
* We can use `this` attribute to visit these reactive content which are declared in data option.
* These instance properties are only added when the instance is first created, ensure those properties exist in data option.
* In Vue 3, data is made reactive by leveraging JavaScript Proxies. `this.xxx`' value is a reactive proxy of the original `xxx`
* the methods option can be used to declare methods.
* The methods option should be an object containing the desired methods.
* In methods option, `this` points the component instance. avoid using arrow functions when defining methods.
* DOM updates aren't applied synchronously. Instead, Vue buffers tem until the 'next tick' in the update cycle. nextTick()
 global API can be used to wait for the DOM update to complete after a state change.
* By default, the reactive state is deeply. It is also possible to explicitly create shallow reactive objects in advanced usage.
* Stateful methods should be declared with lifecycle hook.

### Composition API

* With Composition API, `reactive()` function can be used to create a reactive object or array.

```js
import { reactive } from `vue`

const state = reactive({ count: 0 })
```

* Reactive objects are JavaScript Proxies and behave just like normal objects.
* To use reactive state in a component's template, declare and return them from a component's `setip()` function:
```js
import { reactive } from 'vue'

export default {
    setup() {  
        const state = reactive({ count: 0 })
        
        function increment(){
            state.count++
        }
        return { state, increament }
    }
}
```
```vue
<div @click="increment">{{ state.count }}</div>
```
* When using Single-File Components, we can greatly simplify the usage with `<script setup>`.
```vue
<script setup>
import { reactive } from 'vue'

const state = reactive({ count: 0 })

function increment(){
  state.count++
}
</script> 
```
* The returned value from `reactive()` is a Proxy of the original object.
* Calling `reactive()` on the same object always returns the same proxy, and calling reactive() on an existing proxy also 
returns that same proxy. This rule applies to nested objects as well.
* Two limitations of `reactive()`:
  * work for object types instead of primitive types.
  * can't easily replace and use a reactive object.
* `ref()` function can be used to create reactive "refs" that can hold any types.
* Reactive refs have a `.value` property.
* We can use reactive refs's `.value` peoperty to maintain it's reactivity.
* The Ref's top-level property can be unwrapped in template {{}}.
* The ref as a property of a reactive object is  also automatically unwrapped.
* Ref unwrapping only happens inside a deep reactive object instead of a shallow reactive object.
* Arrays and Collections is no unwrapping feature.
##Computed Properties
