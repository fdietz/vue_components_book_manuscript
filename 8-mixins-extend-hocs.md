# Mixins, extending components and High Order Components

This is the first chapter which doesn't really deal with component composition, but instead focuses on code reuse with various concepts including Mixins, extending existing components and even high order components. We want to focus on mixins first, since this is the simplest concept directly supported by Vue.js and then proceed to more advanced patterns.

Mixins are a way to reuse functionality in multiple Vue components. When a component uses a mixin, all options and functions will be "mixed" or "merged" into the component's own options. We differentiate between global and local mixins, whereas the former is used in the Vue instance.

## Global Mixins

A global mixin is used by the Vue instance using the `mixin` property and makes it's options available to all components used by this Vue instance:

```js
new Vue({ 
  el: '#demo',
  mixins: [MyMixin]
});
```

As an example we want to implement a very simple translation service. It should provide a `translate` function for all components to lookup translation strings.

Let's start with the Vue instance:

```js
new Vue({ 
  el: '#demo',
  mixins: [Translate],
  data: {
    locale: "en"
  }
});
```

And the actual `Translate` mixing:

```js
// some example translations of two languages
const TRANSLATIONS = {
  en: {
    firstName: "Firstname",
    age: "Age"
  },
  de: {
    firstName: "Vorname",
    age: "Alter"
  }
};

const Translate = Vue.mixin({
  methods: {
    translate(key) {
      return TRANSLATIONS[this.$root.locale][key];
    }
  },
  ready() {
    // default locale set to "en"
    this.$root.$set("locale", "en");
  }
});
```

The `translate` function looks for the translated string using the `locale`, configured by the Vue instance and the key. In case the Vue instance does not set the locale, we use the `ready` lifecycle hook to set it ourselves.

By mixing the `Translate` mixin with the Vue instance we can now call it's `translate(key)` function from other components. For our example we create a small card component which renders the first name and age:

```js
Vue.component("card-profile", {
  template: "#card-profile-template",
  props: {
    firstName: String,
    age: String
  }
});
```

And the template uses the `translate` function:

```html
<template id="card-profile-template">  
  <div>
    <h2>My Profile</h2>
    <div>
      {{translate("firstName")}}: {{firstName}}
    </div>
    <div>
      {{translate("age")}}: {{age}}
    </div>
  </div>
</template>
```

With the component in place we can render the demo app:

```html
<div id="demo">
  <card-profile first-name="Michael" age="30" />
  <card-profile first-name="Lana" age="32" />
</div>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-8/example-1)

Global mixins should only rarely be used since all options are mixed into all components which can quickly get out of hand. More often you might want to use a local mixin instead. 

## Local Mixins

A local mixin's options are only merged with the component using this mixin. This makes this approach much more manageable. For our example we want to build a small data loader mixin which loads data via AJAX request from a configurable url.

Here's the mixin:

```js
const DataLoader = Vue.mixin({
  data() {
    return {
      loading: false,
      response: null
    }
  },
  methods: {
    load(url) {
      this.loading = true;
      return axios.get(url)
        .then(response => {
          this.response = response.data;
          this.loading = false;
        })
    }
  }
});
```

It provides a `load` method which uses [axios](https://github.com/axios/axios) again to fetch some data from a remote url. Additionally, the `loading` and `response` data is provided for you.

Let's use this mixin in our component:

```js
Vue.component("article-card", {
  mixins: [DataLoader],
  template: "#article-card-template",
  created() {
    this.load("https://jsonplaceholder.typicode.com/posts/1")
  }
});
```

We use the `mixin` option to use the `DataLoader` and call the `load` function provided by the mixin in the `created` lifecyle hook.

The template for the `article-card` component is showing the data depending on the `loading` state.

```html
<template id="article-card-template">  
  <div>
    <span v-if="loading">Loading...</span>
    <div v-else>
      <h2>{{response.title}}</h2>
      {{response.body}}
    </div>
  </div>
</template>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-8/example-2)

There's one aspect of a mixin we haven't discussed yet. How intelligent is the actual merging strategy? In fact if your component has it's own state `loading` or `response` it would conflict with the mixin state and would certainly cause confusion. Same goes for methods, components and directive options: The component always has priority. When it comes to lifecyle methods, both will be called but the mixin's method will be called first.

There's a whole chapter dedicated to merging strategies in the [Vue.js guide](https://vuejs.org/v2/guide/mixins.html#Option-Merging).

## Extending components

Using a mixin is just one way to reuse code in Vue.js. One other way is to use the `extends` option instead. For our example think of a very complicated component which fetches some user data and renders a pretty user card. You are just reusing the component and cannot change the implementation. But using `extends` you can build a new component which reuses most code of the original component.

Let's have a look at our fancy user card component:

```js
const BaseArticleCard = Vue.component("base-article-card", {
  props: ["id"],
  template: "#base-article-card-template",
  data() {
    return {
      loading: false,
      title: "",
      body: "",
      userId: ""
    }
  },
  computed: {
    articleTitle() { 
      return `Article: ${this.title}`;
    }
  },
  methods: {
    load(id) {
      this.loading = true;
      return axios.get(`https://jsonplaceholder.typicode.com/posts/${id}`)
        .then(response => {
          this.title = response.data.title;
          this.body = response.data.body;
          this.userId = response.data.userId;
          this.loading = false;
        });
    }
  },
  created() {
    this.load(this.id);
  }
});
```

I've used very similar code again to fetch the data via [axios](https://github.com/axios/axios) request library in the `created` hook via a `load` method. An `id` prop is used to identify the user for the request url.

The template then renders the user data depending on the loading state:

```html
<template id="base-article-card-template">  
  <div class="article-card">
    <div v-if="loading">Loading...</div>
    <div v-else>
      <h2>{{title}}</h2>
      {{body}}
    </div>
  </div>
</template>
```

Now, imagine your own card component renders things slightly different. We can simply extend the component:

```js
Vue.component("advanced-article-card", {
  extends: BaseArticleCard,
  template: "#advanced-article-card-template"
});
```

And then use a different template:

```html
<template id="advanced-article-card-template">  
  <div class="article-card">
    <div v-if="loading">Loading...</div>
    <div v-else>
      <h2>{{articleTitle}}</h2>
      <p>Written by User ID: {{userId}}</p>
      {{body}}
    </div>
  </div>
</template>
```
I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-8/example-3)

All options of the base component are reused in our new component, except the template where we decided to use our own. 

D> To be honest with you, I had quite a hard time to come up with a sensible example for the `Vue.extend` D> feature. In almost all cases I come up with I personally prefer using mixins instead.

## High Order Components

HOCs are components which return another component but extend the behaviour in a reuseable way. Our data loading component is again a good example to get our feet wet with High Order components. So, we want to extend the card component from the previous example with a HOC which fetches data and passes this data along to the component via props.

In our next example we need to use the `vue-cli` to generate a project using SFC.

The usage of a HOC looks like this:

```js
// our component
import ArticleCard from "./components/ArticleCard.vue";
// the HOC function
import withLoader from "./withLoader";
// our combined resulting component
const ArticleCardWithLoader = withLoader(ArticleCard);
```

We implement a `withLoader` function which gets an `ArticleCard` component as input and returns the extended component `ArticleCardWithLoader`.

The implementation of the HOC looks similar to our previous examples:

```js
// withLoader.js
import Vue from "vue";
import axios from "axios";

const withLoader = component => {
  return Vue.component("with-loader", {
    render(createElement) {
      return createElement(component, {
        props: {
          loading: this.loading,
          title: this.title,
          body: this.body
        }
      });
    },
    props: ["id"],
    data() {
      return {
        loading: false,
        title: "",
        body: "",
      }
    },
    methods: {
      load(id) {
        this.loading = true;
        return axios.get(`https://jsonplaceholder.typicode.com/posts/${id}`)
          .then(response => {
            this.title = response.data.title;
            this.body = response.data.body;
            this.loading = false;
          });
      }
    },
    created() {
      this.load(this.id);
    }
  });
};

export default withLoader;
```

The `withLoader` function returns a new component which wraps our component and passes along some props including `loading`, `title` and `body` in the `render` function.

Our card component can then render this data, without knowing that it is wrapped by a HOC component:

```html
<template>
  <div class="article-card">
    <div v-if="loading">Loading...</div>
    <div v-else>
      <h2>{{title}}</h2>
      {{body}}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    loading: Boolean,
    title: String,
    body: String
  }
};
</script>
```

I> You can find the complete example on [Github](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-8/example-4)

There are a lot of open questions here. For example in the current implementation we cannot just pass along some additional props without changing the HOC component since it needs to pass these props
explicitly . It's not really a problem for such a small example, but it shows that there is no easy way to compose components using generics HOCs with Vue.js built-in functionality.

## Summary

There's an ongoing discussion in the Vue.js community about the use of HOCs, or High Order Components. These are quite popular in the React community actually.

If you are interested in the discussion and props and cons of using HOCs in Vue.js, I can highly recommend this [article](https://medium.com/bethink-pl/do-we-need-higher-order-components-in-vue-js-87c0aa608f48)
by Bogna Knychała and the [vue-hoc](https://github.com/jackmellis/vue-hoc/blob/master/packages/vue-hoc/README.md) project which implements some useful helpers to work around these problems.

D> Personally, I prefer to use slots and scoped slots instead of HOCs but your mileage may vary.
