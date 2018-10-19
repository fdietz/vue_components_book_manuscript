# Well behaving Vue.js components

Until now most tips are very generic, in the sense that they can be applied to all component based frameworks including React or Angular. Let's know look into some Vue.js specific tips!

https://vuejsdevelopers.com/2018/06/18/vue-components-play-nicely/

## Implement `v-model` compatibility

For components that are essentially form fields, which encapsulate a single value which can be changed by the user, it is important to support `v-model`. `v-model` works by passing in 
a `value` prop and applying an `input` event handler. 

## Use events instead of callbacks

Similar to React we can pass a function as a prop which the child can use as a callback. It is similar to an event, but it is hard to differentiate props which send data to a component
from props which get data from a parent. Usually, this is solved via a naming convention, as in this example:

```js
export default {
  props: ['onEventFired']
  methods() {
    handleAction() {
      if (this.onEventFired) {
        this.onEventFired(this.data);
      }
    }
  }
}
```

And then it is used like this:

```html
<my-component :on-event-fired="handleAction" />
```

Instead it is better to use Vue.js events:

```js
export default {
  methods() {
    handleAction() {
      ... // your custom code
      this.$emit('action-fired', data);
    }
  }
}
```

Which then looks like this:

```html
<my-component @action-fired="handleAction" />
```

## Transparent event handling

But, what about all the other events besides the `input` event. Thinks like `click` events, keyboard handling, etc.? Native events bubble up the DOM, but Vue.js events do not bubble up by 
default.

For example this will not work:

```html
<PrimaryButton @click="handleClick">Click me</PrimaryButton>
```

Unless we implement some code in the `<PrimaryButton>` component which emits the `click` event. Since it is quite cumbersome to manually implement all possible event handlers, Vue.js gives us 
a way to programmatically access all listeners of a component with the `$listeners` object.

For example, our primary button component looks like this:

```html
<button v-on="$listeners">
  </slot>
</button>
```

Now, every event handler on the button is passed up correctly.

## Attribute handling

Similar to events we also have to support other attributes. By default, Vue.js takes all the attributes and applies them to the root element of the component. But, what happens in cases where
you don't want this default behaviour?

Let's imagine a `<MagicTextArea>` component which wraps a `textarea` element:

```html
<div class="magic-wrapper">
  <textarea></textarea>
</div>
```

In this example, Vue.js would apply all the attributes specific to the `textarea` to the root, the `div` instead. Not very useful!

To change this default behaviour, we need to tell Vue.js not to apply the attributes by default:

```js
export default {
  inheritAttrs: false
}
```

And then apply them manually via the `$attrs` object:

```html
<div class="magic-wrapper">
  <textarea v-bind="$attrs"></textarea>
</div>
```

## Summary

Vue.js offers some nice defaults when it comes to implementing components. But,it is good to know when and how to manually change the default behaviour! Do you have even more tips?