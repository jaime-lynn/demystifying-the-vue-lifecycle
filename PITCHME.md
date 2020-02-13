## Demystifying the Vue Lifecycle and other pieces of the Vue instance

An overview of the Vue lifecycle with examples of when to use each lifecycle method. Also examines various pieces of the Vue instance and how to use them effectively and the pitfalls associated. Examples would be things like v-for key and the pitfalls of just using index, $refs and when to use them, and $nextTick and the problems it can help solve.

---

### Title Slide
- Introduce self and topic ("We're going to be talking about the Vue lifecycle and X [if there's time]")

---

### What is the Vue Lifecycle?
- Begins when the Vue instance (your component) initializes
- Has a cycle it goes through as observable data changes
- Has an end when a component is destroyed

---

### The Lifecycle Diagram
- Discuss the lifecycle diagram from the Vue docs
- Note that if you are using the Composition API, `setup` is called after `beforeCreate` and right before `created`

---

### Lifecycle Hooks
- Example usage - note that you cannot use arrow functions as they break the binding of `this`
- A lot of hooks: beforeCreate, created, beforeMount, mounted, beforeUpdate, updated, activated, deactivated, beforeDestroy, destroyed, errorCaptured

---

### beforeCreate
> Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.

You will have access to the instance itself! But you will _not_ have access to any of the data or events on the instance.

```javascript
export default {
  data() {
    return {
      foo: 'bar'
    }
  },
  beforeCreate() {
    console.log(this);
    // This will work and will return the component instance
    console.log(this.foo);
    // This will return undefined as data has not yet been initialized 
  }
}
```

---

### created
> Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following has been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the `$el` property will not be available yet.

You now have access to data, so this is the ideal time to populate any initial data your component needs!

```javascript
export default {
  data() {
    return {
      cookies: []
    }
  },
  created() {
    this.cookies = axios({ /* Call and get the cookies! */ })
  }
}
```

---

### beforeMount
> Called right before the mounting begins: the `render` function is about to be called for the first time. **This hook is not called during server-side rendering.**

```javascript
const app = new Vue({
  el: "#app",
  data() {
    return {
      foo: "bar"
    };
  },
  template: "<div>{{foo}}</div>",
  beforeMount() {
    console.log(this.$el); // <div id="app" data-fizz="buzz"></div>
    console.log(this.$el.dataset.fizz); // buzz
  },
  mounted() {
    console.log(this.$el); // <div>bar</div>
    console.log(this.$el.dataset.fizz); // undefined
  }
});
```

```html
<div id="app" data-fizz="buzz"></div>
```

---

### mounted
> Called after the instance has been mounted, where `el` is replaced by the newly created `vm.$el`. If the root instance is mounted to an in-document element, `vm.$el` will also be in-document when `mounted` is called.
> Note that mounted does **not** guarantee that all child components have also been mounted. If you want to wait until the entire view has been rendered, you can use `vm.$nextTick` inside of `mounted`:
```javascript
mounted() {
  this.$nextTick(function() {
    // Code that will run after the
    // entire view has been rendered
  })
}
```
> **This hook is not called during server-side rendering.**

Ideal for integrating libraries

---

### beforeUpdate
> Called when data changes, before the DOM is patched. This is a good place to access the existing DOM before an update, e.g. to remove manually added event listeners.
> **This hook is not called during server-side rendering, because only the initial render is performed server-side.**

---

### updated
> Called after a data change causes the virtual DOM to be re-rendered and patched.
> The component's DOM will have been updated when this hook is called, so you can perform DOM-dependent operations here. However, in most cases you should avoid changing state inside the hook. To react to state changes, it's usually better to use a computed property or watcher instead.
> Note that `updated` does **not** guarantee that all child components have also been re-rendered. If you want to wait until the entire view has been re-rendered, you can use `vm.$nextTick` inside of `updated`:
```javascript
updated() {
  this.$nextTick(function() {
    // Code that will run only after the
    // entire view has been re-rendered
  })
}
```
> **This hook is not called during server-side rendering.**

---

### activated
> Called when a kept-alive component is activated.
> **This hook is not called during server-side rendering.**

---

### deactivated
> Called when a kept-alive component is deactivated.
> **This hook is not called during server-side rendering.**

---

### beforeDestroy
> Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.
> **This hook is not called during server side rendering.**

---

### destroyed
> Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.
> **This hook is not called during server-side rendering.**

---

### errorCaptured