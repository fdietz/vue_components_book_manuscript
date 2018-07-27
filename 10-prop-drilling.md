# The Prop Drilling Problem

Prop drilling is not really a pattern, but an actual problem. It describes a situation with multiple nested components where each component passes along the same props. This leads to hard to maintain code, since changing the props leads to changes in all the components of this nested hierarchy.

## Example with nested components

Let's imagine a page component, which contains a page header component, which itself contains a header menu which itself contains a user avatar:

```html
<div id="demo">
  <page-layout :user="user"></page-layout>
</div>

<template id="page-layout-template">
  <div class="page-layout">
    <page-layout-header :user="user"></page-layout-header>
  </div>
</template>

<template id="page-layout-header-template">
  <div class="page-layout-header">
    <header-menu :user="user"></header-menu>
  </div>
</template>

<template id="header-menu-template">
  <div class="header-menu">
    <user-avatar :user="user"></user-avatar>
  </div>
</template>

<template id="user-avatar-template">
  <div class="user-avatar">
    <div>{{user.name}}</div>
  </div>
</template>
```

Only the `user-avatar-template` is actually making use of the `user` prop.

And here are all components:

```js
Vue.component("page-layout", {
  template: "#page-layout-template",
  props: ["user"]
});
Vue.component("page-layout-header", {
  template: "#page-layout-header-template",
  props: ["user"]
});
Vue.component("header-menu", {
  template: "#header-menu-template",
  props: ["user"]
});
Vue.component("user-avatar", {
  template: "#user-avatar-template",
  props: ["user"]
});

new Vue({ 
  el: '#demo',
  data: {
    user: { id: 1, name: "Donald Duck" }
  }
});
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-9/example-1)

The Vue instance has the `user` state and passes this user as a prop to the `page-layout` component. You can tell that even a simple user case like renaming the user props, means we have to touch each component definition and template.

The solution is in this case to use slots and move the creation of the component hierarchy into the top level:

```html
<div id="demo">
  <page-layout>
    <page-layout-header>
      <header-menu>
        <user-avatar :user="user"></user-avatar>
      </header-menu>
    </page-layout-header>
  </page-layout>
</div>
```

Note, how only the `user-avatar` component makes use of the `user` prop. All other components use slots:

```html
<template id="page-layout-template">
  <div class="page-layout">
    <slot></slot>
  </div>
</template>

<template id="page-layout-header-template">
  <div class="page-layout-header">
    <slot></slot>
  </div>
</template>

<template id="header-menu-template">
  <div class="header-menu">
    <slot></slot>
  </div>
</template>

<template id="user-avatar-template">
  <div class="user-avatar">
    <div>{{user.name}}</div>
  </div>
</template>
```

And here's the component definition:

```js
Vue.component("page-layout", {
  template: "#page-layout-template"
});
Vue.component("page-layout-header", {
  template: "#page-layout-header-template"
});
Vue.component("header-menu", {
  template: "#header-menu-template"
});
Vue.component("user-avatar", {
  template: "#user-avatar-template",
  props: ["user"]
});
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-9/example-2)

## Summary

In many cases where you think the complexity might justify integrating [vuex](https://vuex.vuejs.org/) for state management or other approaches, such a refactoring can really simplify your code.