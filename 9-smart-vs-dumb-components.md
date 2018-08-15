# Smart vs Dumb Components

In the previous chapter we looked into how to organize code for reuse, using Mixins and other means. There's another option which might help in structuring your components and the code.

The generall idea is to separate one large component which combines business logic and presentation into two separate components. The "smart" component is only responsible for the business logic, as for example fetching data, transforming and other actions. The "dumb" component accepts props and is only responsible for rendering the content. Sometimes these are also called "container" vs "presentational" components.

[Dan Abramov](https://twitter.com/dan_abramov) was the first who wrote about this concept in his article [Smart vs Dumb Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) in the context of React.

Let's have a look at an example on how to implement this pattern in Vue.js, using a list of users.

## List of Users as smart and dump components

We start with the dump component which renders a list of users:

```js
Vue.component("user-list", {
  props: ["users"],
  template: "#user-list-template"
});
```

The template renders the users array:

```html
<template id="user-list-template">
  <ul>
    <li v-for="user in users" :key="user.id">
      {{user.name}}
    </li>
  </ul>
</template>
```

The smart component is responsible for providing the `users` prop to the `user-list` component:

```html
<template id="user-list-container-template">
  <user-list :users="users"></user-list>
</template>
```

To simplify the example we use a list of users as state only in the smart component:

```js
Vue.component("user-list-container", {
  template: "#user-list-container-template",
  data() {
    return {
      users: [
        { id: 1, name: "Donald Duck" },
        { id: 2, name: "John Doe" },
        { id: 3, name: "Sherlock Holmes" }
      ]
    }
  }
});
```

With everything in place the Vue instance can be rendered:

```html
<user-list-container></user-list-container>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-9/example-1)

Separating a component into two cleanly separate components provides more flexibility. We can for example change one component without touching the other component.

## Summary

But, this is by no means a best practice one should apply in all scenarios. The additional complexity due to separation of two concerns must be justified. So, only use this pattern if your component becomes too complicated.

Btw, Dan Abramov himself is almost regretting that he wrote this article in this [Twitter discussion](https://twitter.com/dan_abramov/status/802569801906475008), since the pattern was almost understood as a hard rule and applied in too many places.