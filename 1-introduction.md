# Welcome

Vue.js is a progressive web framework for building user interfaces created by Evan You. It is designed to be incrementally adoptable and
therefore easy to pick up in existing projects. But, on the other hand capable of powering sophisticated applications.

This book assumes that the reader has some basic understanding of Vue.js. It builds on that knowledge to give the reader
a deeper understanding of the Vue.js component model and discusses advanced component patterns.

If you have already heard of Vue.js scoped lots, renderless components or high order components but were unsure when or how
to use these concepts, you are in the right place here!

If you dont' know your way around Vue.js yet, I can highly recommend the official [Vue.js guide](https://vuejs.org/v2/guide/index.html).
But, please don't be afraid of diving right into this book with me. I will point you to the relevant official documentation in
the various chapters to give you some more background and a chance to read up on some more background material.

Many concepts introduced in this book have similar occurrences in other web frameworks as for example React or Angular. If you
have experience in either one of these, you will be delighted to learn that all your existing knowledge can be easily applied
to Vue.js too.

## Tooling and Code Examples

All example code can be found in the public [GitHub repository](https://github.com/fdietz/vue_components_book_examples) accompanying the book.

Especially for the exercises you will always find a `start.html` and `solution.html` or directories named accordingly.

## Structure

Chapter 2 will start with a high level introduction of components and their properties. I will provide the mental model for all
the upcoming chapters.

In chapter 3 we will discuss the Vue.js concept of `slots` and `named slots` for component composition.

Chapter 4 introduces `scoped slots` and which problems you can solve with them.

In chapter 5 we discuss headless components which provide high flexibility by separating logic from rendering.

Chapter 6 introduces functional components, useful when you don't need state or lifecycle in your component.

Dynamic component rendering in chapter 7 shows how to use the `<component is>` pattern, a built-in Vue.js feature.

In chapter 8 we switch focus to code reuse using mixins and high order components.

Chapter 9 introduces a concept where a complex component is separated into a smart and dumb component to provide more flexibility.

The problem of "prop drilling" is explained in chapter 10 and one possible solution is offered with using more advanced state management.

Chapter 11 introduces the provide/inject API useful for plugin and component authors who deal with similar problems.

In chapter 12 we discuss loading components asynchronously and how to handle loading and error state.

## Images used in this book

Most images are are from [unsplash.com](unsplash.com), as for example:

* [The cat](https://unsplash.com/photos/V7RugxejXH)
* [Another cat](https://unsplash.com/photos/FT9SsFEXqF4)
* [Cat 3](https://unsplash.com/photos/_FoHMYYlatI)
