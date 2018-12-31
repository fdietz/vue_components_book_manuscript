# Provide/Inject

When you work on your own components, libraries or plugins for Vue.js you might encounter situations where prop drilling might be the only way to pass along a global value down the component hierarchy. In such a case the Provide/Inject API might be useful. 

It consists of two parts: The "provide" part which exposes some values and the "inject" part which uses these values. The provider of the value is usually high up in the component hierarchy, whereas the inject part is some child component.

As an example we want to look into building our own component library, with for example a button component. The component library should be themed with a light and dark theme. 

Let's start with the button component which uses the current theme to change it's styling:

```html
<template id="themed-button-template">
  <button :class="['button', theme.name]">
    <slot></slot>
  </button>
</template>
```

The button binds the class `button` and the name of the theme, so depending on the dark or light theme with end up with the following output:

```html
<button class="button dark">Click me</button>
<button class="button light">Click me</button>
```

And the component definition uses the `inject` option to access the `theme` object:

```js
Vue.component("themed-button", {
  template: "#themed-button-template",
  inject: ["theme"]
}); 
```

How do we expose this theme object? In our case we can provide the `theme` right in the Vue instance:

```js
new Vue({ 
  el: '#demo',
  provide() {
    return {
      theme: this.theme
    }
  },
  data: {
    theme: {
      name: "dark"
    }
  }
});
```

First we define the state of the `theme` object with the name and then we use the `provide` option and return the theme.

In our example it would be cool if the theme could be switched live:

```html
<div id="demo">
  <select v-model="theme.name">
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
  <themed-button>Click me</themed-button>
</div>
```

That's it already. When you select a different theme the button changes it's styling.

You might be wondering about one thing though. Why not just expose the name of the theme as a string directly, instead of using a theme object with a name? 

By itself provide/inject are not reactive, so the child would not know if the theme was updated.

Here is the same example but with a string:

```js
new Vue({ 
  el: '#demo',
  provide() {
    return {
      theme: this.theme
    }
  },
  data: {
    theme: "dark"
  }
});
```

The child will not be notified about the changed theme name. But, that might actually be okay in some cases. For example, you might want to provide some global translated validation messages which never change anyways.

## Summary

Provide/inject is a powerful feature for library or plugin authors. It is not recommended to use in generic application code.