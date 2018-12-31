# Async Components

In this chapter we look into how to load components asynchronously to optimize the loading of a website. 

The more components you use, the larger becomes your Javascript payload. Meaning your users have to wait longer before they can start using your website.

If you draw a picture of all components, their child components, and again their child components you would end up with a tree connecting all components. The first component of the tree would be your Vue instance. But, when you think about it, the first thing the user will see on your website does not really require all components. It only requires the components which are used on this first page.

So, our goal should be to only load what's required for a page and to asynchronously load more components on demand.

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
// leanpub-start-insert
const AsyncComponent = import("./components/HelloWorld.vue");
// leanpub-end-insert
export default {
  name: "app",
  components: {
    // leanpub-start-insert
    HelloWorld: () => ({
      component: AsyncComponent
    })
    // leanpub-end-insert
  }
};
```

We use the `import` function to load the component instead of a static module import. Additionally, the syntax to specify the components of our Vue instance changed to return a function with a `component` option.

Now, if you refresh the page the component should be loaded instantly and you should see an additional request in your browser which loads the component.

But, the async component support of Vue.js offers some more niceties. We can for example specify a component to show during the loading. And we can specify an error component, in case something goes wrong.

```js
// leanpub-start-insert
import LoadingComponent from "./components/LoadingComponent";
import ErrorComponent from "./components/ErrorComponent";
// leanpub-end-insert
const AsyncComponent = import("./components/HelloWorld.vue");

export default {
  name: "app",
  components: {
    HelloWorld: () => ({
      component: AsyncComponent,
      // leanpub-start-insert
      loading: LoadingComponent,
      error: ErrorComponent
      // leanpub-end-insert
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

Additionally, we can further customize the loading with an initial `delay` option before showing the loading component. In case the loading happens very quickly, we don't need to show the loading component at all. Further an `timeout` option will show the error component if our component was not loaded on time.

```js
import LoadingComponent from "./components/LoadingComponent";
import ErrorComponent from "./components/ErrorComponent";
const AsyncComponent = import("./components/HelloWorld.vue");

export default {
  name: "app",
  components: {
    HelloWorld: () => ({
      component: AsyncComponent,
      loading: LoadingComponent,
      error: ErrorComponent
      // leanpub-start-insert
      // optional delay before showing the loading component
      delay: 200
      // optional timeout before showing the error component
      timeout: 5000
      // leanpub-end-insert
    })
  }
};
```

## Summary

The community talks a lot about code splitting and reducing the initial payload. It has become quite important especially with the rise of mobile phones usage and low bandwidth networks. In this chapter we introduced one way to load components asynchronously and looked into advanced problems like loading and error state handling.