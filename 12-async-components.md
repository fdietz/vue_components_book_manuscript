# Async Components

In this chapter we look into how to load components asynchronously to optimize the loading of a website. 

The more components you use, the larger your Javascript payload becomes. Meaning your users have to wait longer before they can start using your website.

If you draw a picture of all components, their child components, and again their child components you would end up with a tree connecting all components. The first component of the tree would be your Vue instance. But, when you think about it, the first thing the user will see on your website does not really require all components. It only requires the components which are used on this first page.

So, our goal should be to only load what's required for a page and to asychronously load more components on demand.

Vue.js has built-in support to load components asynchronously together with [Webpack](https://webpack.js.org/).

Let's create an example project using the `vue-cli` with default settings. Our `App.vue` file typically looks like this:

```html
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "app",
  components: {
    HelloWorld
  }
};
</script>
```

To load the `HelloWorld` component async we have to change how it is imported:

```js
const AsyncComponent = import("./components/HelloWorld.vue");
export default {
  name: "app",
  components: {
    HelloWorld: () => ({
      component: AsyncComponent
    })
  }
};
```

We use the `import` function to load the component instead of a static module import. Additionally, the syntax to specify the components of our Vue instance changed to return a function with a `component` option.

Now, if you refresh the page the component should be loaded instantly and you should see an additional request in your browser which loads the component.

But, the async component support of Vue.js offers some more niceties. We can for example specify a component to show during the loading. And we can specify and error component, in case something goes wrong.

```js
import LoadingComponent from "./components/LoadingComponent";
import ErrorComponent from "./components/ErrorComponent";

const delayedAsyncComponent = () => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(import("./components/HelloWorld.vue"));
    }, 2000);
  });
};

export default {
  name: "app",
  components: {
    HelloWorld: () => ({
      component: delayedAsyncComponent(),
      loading: LoadingComponent,
      error: ErrorComponent
      // optional delay before showing the loading component
      // delay: 200
      // optional timeout before showing the error component
      // timeout: 0
    })
  }
};
```

The `LoadingComponent` and the `ErrorComponent` must be imported statically and are for demonstration purposes very simple:

```html
<template>
  <div>
    Loading component...
  </div>
</template>
```

Instead of directly importing the component as in the previous example, I've used a `setTimeout` call wrapped in a Promise instead. This way we can actually see the loading component for two seconds before the actual loading starts.

Additionally, we can further customize the loading with an initial `delay` option before showing the loading component. In case the loading happens very quickly, we don't need to show the loading component at all. Further an `timeout` option will show the error component if our component was not loaded on time.