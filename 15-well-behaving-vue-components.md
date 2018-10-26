# Well behaving Vue components

After this whirlwind tour of the Vue component model, I'd like to look into some tips for implementing Vue components.

## Implement `v-model` compatibility

For components that are essentially form fields, meaning they encapsulate a single value which can be changed by the user, it is important to support `v-model`. `v-model` works by passing in 
a `value` prop and applying an `input` event handler. It is a directive to create two-way data bindings on various kind of inputs.

Instead of manually declaring a `value` prop and `input` event like this:

```html
<input
  :value="searchText"
  @input="searchText = $event.target.value"
>
```

One can use `v-model`:

```html
<input v-model="searchText">
```

The official Vue guide contains a whole section dedicated to [form input bindings](https://vuejs.org/v2/guide/forms.html) including the use of `v-model`.

## Use events instead of callbacks

Similar to React we can pass a function as a prop which the child can use as a callback. It is similar to an event, but it is hard to differentiate props which send data to a component
from props which return data from a component. Usually, this is solved via a naming convention, as in this example:

```js
export default {
  props: ['onEventFired']
  methods() {
    handleAction() {
      if (this.onEventFired) {
        this.onEventFired(this.data.payload);
      }
    }
  }
}
```

The `onEventFired` prop is a function which is called in the `handleAction` function and passes along the `payload` data.

And then it is used like this:

```html
<my-component :on-event-fired="handleIt" />
```

Without enforcing a naming convention like the `on` prefix for the prop name, it would be impossible to know if this prop is used as a call back.

Therefore it is better to use Vue events:

```js
export default {
  methods() {
    handleAction() {
      this.$emit('action-fired', this.data.payload);
    }
  }
}
```

Which then looks like this:

```html
<my-component @action-fired="handleIt" />
```

So, even though you can achieve the same result using both techniques, I recommend using events to follow the Vue community conventions.

## Transparent event handling

But, what about all the other events besides the `input` event. Things like `click` events, keyboard handling, etc.? Native events bubble up the DOM, but Vue events do not bubble up by 
default.

For example this will not work:

```html
<!-- component definition -->
<button>
  </slot>
</button>

<!-- usage -->
<PrimaryButton @click="handleClick">Click me</PrimaryButton>
```

We must implement some code in the `<PrimaryButton>` component to emit the `click` event. Since it is quite cumbersome to manually implement all possible event handlers, Vue gives us 
a way to programmatically access all listeners of a component with the `$listeners` object.

For example, our primary button component looks like this:

```html
<button v-on="$listeners">
  </slot>
</button>
```

Now, every event handler on the button is passed up correctly.

## Attribute handling

Similar to events we also have to support other attributes. By default, Vue takes all the attributes and applies them to the root element of the component. But, what happens in cases where
you don't want this default behaviour?

Let's imagine a `<MagicTextArea>` component which wraps a `textarea` element:

```html
<!-- component definition -->
<div class="magic-wrapper">
  <textarea></textarea>
</div>

<!-- usage -->
<MagicTextArea rows="4" cols="20">
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

Vue offers some nice defaults when it comes to implementing components. But, it is good to know when and how to manually change the default behaviour! Do you have even more tips?