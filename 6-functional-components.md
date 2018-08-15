# Functional components

In most Vue.js apps there are a lot of components which do not really do anything besides rendering a template. They do not contain any business logic or make use of the component lifecycle.

In this case using a functional component might remove some unnecessary boilerplate and the component renders faster too! 

You can think of a functional component to be the equivalent of a function which takes a render context as input and returns rendered HTML.

In this chapter we explore how and when to use functional components and the pros and cons of them.

## Functional components using the vue-cli and SFC

Let's start with a new default project created via the [vue-cli](https://cli.vuejs.org/), following the [offical guide](https://cli.vuejs.org/guide/creating-a-project.html#installation)
using the default setup.

It should generate an `App.vue` and a `HelloWorld.vue` file for you which we start modifying for our example.

The `App.vue` file imports the `HelloWorld` component which has a `msg` prop and a `@click` event. We use this event to increment a `clickCount`.

```html
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App" @click="clickCount+=1">
      <p>I was clicked: {{clickCount}}</p>
    </HelloWorld>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "app",
  data() {
    return {
      clickCount: 0
    };
  },
  components: {
    HelloWorld
  }
};
</script>
```

The `HelloWorld` component consists of a template only:

```html
<template functional>
  <div class="hello">
    <h1>{{ props.msg }}</h1>
    <button @click="listeners.click">Click me</button>
    <slot></slot>
  </div>
</template>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-6/example-1)

Note, that the template has an additional `functional` attribute. This flag turns the component into a functional component. Additionally, Vue.js changes how you access the context of the component. Where you previously accessed props like `msg` directly, you now need to use `prop.msg` instead and events via `listeners.click`. 

All these changes in usage are necessary since a functional component has no instance or state and therefore no `this` or `data`.

If you need to create lots of small mainly visual components, as for example a Heading, functional components make a lot of sense.

## Functional components using Vue.component and render function

There is another way of using functional components using the `Vue.component` function:

```js
Vue.component("hello-world", {
  // leanpub-start-insert
  functional: true,
  // leanpub-end-insert
  render(createElement, {data, listeners, slots}) {
    return createElement("div", { class: "hello" }, [
      createElement('h2', data.attrs.msg),
      createElement('button', { 
        on: { 
          click: listeners.click 
        } 
      }, 'Click me'), 
      slots().default
    ]);
  }
});
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-6/example-2)

The `functional` attribute is used together with a `render` function. We've looked into these `render` functions before in the previous chapter about headless components.

Every argument passed along to the render function is what we call the render context. It includes data, listeners, props, slots, parent, injections, etc. In our example we used Javascript destructuring to only pick what we need in our function. You can read more background about render functions in the official [Vue.js guide](https://vuejs.org/v2/guide/render-function.html).

Compared to the first example using SFC it looks like a lot of boilerplate code. But, this can be much cleaner when using JSX instead.

## Functional components using Vue.component and JSX

In order to use JSX we recommend to use the `vue-cli` with the default setup from the first example. It supports JSX out of the box - no configuration required!

Let's have a look how our component looks like now:

```html
<script>
export default {
  name: "HelloWorld",
  functional: true,
  render(h, { data, listeners, children }) {
    return (
      <div class="hello">
        <h1>{data.attrs.msg}</h1>
        <button onClick={listeners.click}>Click me</button>
        {children}
      </div>
    );
  }
};
</script>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-6/example-3)

Since we use a SFC component again we use a `script` tag for the Javascript code. The `functional` attribute together with the `render` function is used again, but this time the `render` implementation is using JSX syntax. 

When compared to normal Vue.js templates we use single curly braces instead of the mustache syntax and for events we use `onClick` instead of `v-on:click`. But, this is only scratching the surface here. The interesting thing about JSX is that everything in these curly braces is all Javascript and converted to Javascript functions via the `h` argument.

Here's an example of rendering a list in JSX:

```js
const listItems = props.numbers.map(number =>
  <li>{number}</li>
);
return (
  <ul>{listItems}</ul>
);
```

More on JSX syntax in the [Vue.js guide](https://vuejs.org/v2/guide/render-function.html#JSX).

## Summary

I'm not recommending to always use JSX now, but it certainly has it strengths for some use cases and it is therefore beneficial to know the limitations of the Vue.js template language and the pros and cons of JSX compared to that. 

D> I personally favor using Vue.js templates for almost all components. The only reason for me to use JSX is D> when dealing with very dynamic component creation where the number of `v-if` and `v-else` makes the code D> less readible.