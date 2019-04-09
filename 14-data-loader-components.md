# Data Loader Components

In chapter 5 we only discussed headless components for visual component composition, but we can actually go much further. Nothing stops us to use a component to provide some data to the underlying component.

In this example we use the [axios](https://github.com/axios) HTTP client npm package to do an AJAX request and pass the result along.

## The `axios` HTTP client library

To execute an HTTP request we use the `get` function and pass the url along:

```js
axios.get("https://jsonplaceholder.typicode.com/posts/1")
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error);
  })
```

For this free public API the JSON response would be:

```js
{
  userId: 1,
  id: 1,
  title: "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  body: "quia et suscipit suscipit recusandae consequuntur expedita et cum reprehenderit molestiae ut ut quas totam nostrum rerum est autem sunt rem eveniet architecto"
}
```

To access for example the `title` attribute we use the following code:

```js
console.log(response.data.title);
```

Armed with this knowledge we can now implement a reusable component.

## HTTP Request Component

Let's have a look at the example usage first:

```html
<request url="https://jsonplaceholder.typicode.com/posts/1">
  <template v-slot:default="{ loading, data }">
    <div v-if="loading">Loading...</div>
    <div v-if="!loading">
      {{data.title}}
      {{data.body}}
    </div>
  </template>
</request>
```

The `request` component uses the `url` prop to decide which URL we want to request.

The scoped slot passes not only the result back as `data` but also gives us a `loading` prop which we use to show a loading indicator while the request is in the loading state. Only after the loading is done we show the response data.

```js
Vue.component("Request", {
  render() {
    return this.$scopedSlots.default({
      loading: this.loading,
      data: this.response && this.response.data
    })[0];
  },
  props: {
    url: String
  },
  data() {
    return {
      loading: true,
      response: null
    }
  },
  created() {
    axios.get(this.url)
      .then(response => {
        this.response = response;
        this.loading = false;
      })
  }
});
```

I> You can find the complete example on [GitHub](https://github.com/fdietz/vue_components_book_examples/tree/master/chapter-14/example-1)

The `Request` defines the `url` props as it's only configuration and maintains the `response` and the `loading` state. The `created` lifecycle hook is used to fire the HTTP GET request and places the successful response in the response state while also changing `loading` to `false`. And finally the `render` function passes this information along via the scoped slot mechanism.

## Summary

This pattern of co-locating the data with the component is actually not far fetched and used by other libraries extensively. You can see examples in the wild as for example in [vue-apollo](https://github.com/Akryum/vue-apollo), the Vue.js Apollo GraphQL client, where a `Query` and `Mutation` component handle data requirements. 

Here's an example from the documentation:

```html
<div class="users-list">
  <!-- Apollo Query -->
  <ApolloQuery :query="require('@/graphql/users.gql')">
    <!-- The result will be automatically updated -->
    <template v-slot:default="{ result: { data, loading } }">
      <!-- Some content -->
      <div v-if="loading">Loading...</div>
      <ul v-else>
        <li v-for="user of data.users" class="user">
          {{ user.name }}
        </li>
      </ul>
    </template>
  </ApolloQuery>
</div>
```