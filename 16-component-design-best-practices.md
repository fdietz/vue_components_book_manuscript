# Component Design Best Practices

We wrap things up with a final question: When is a component well designed? And what does that include? In this chapter
we want to discuss the principles of component composition.

## Avoid speculative generality

Let's start with the most obvious, but also most difficult one for a typical developer: Solve the problem at hand, instead of trying to solve a potential future problem.

The [Pareto principle](https://en.wikipedia.org/wiki/Pareto_principle), also known as the 80/20 rule, states that, for many events, roughly 80% of the effects come from 20% of the causes.
In other words, you might waste a lot of time fine tuning your components for some anticipated usage, which will never come.

A component should do one thing and do it well.

## Simple API

Your goal is to find the sweet spot between simplicity and flexibility: too simple and it doesn't cover all use cases, to flexible and it becomes too difficult to understand and use.

Typically there's a tradeoff between simplicity and flexibility, but the general idea is to keep a minimal API surface area. The fewer things your consumers need to learn, the easier 
it is to start using your component.

### Reduce the number of props

Try to reduce the number of used props. Don't be afraid to create new components, instead of adding more props to an existing components. For example, a button component might accept multiple 
properties for different colors, sizes, shapes and logic. But, there's usually not always the need to have every combination of those props. Instead, consider creating multiple 
button components, as for example a `PrimaryButton`, `SecondaryButton`, `OutlineButton`, etc. 

```html
<!-- bad -->
<Button primary outline large shadow>Share</Button>

<!-- good -->
<PrimaryButton>Share</PrimaryButton>
```

Another technique to reduce the number of props is to use composition. Instead of adding another prop for an icon and a label, use slots to let consumers compose components.

```html
<!-- bad -->
<PrimaryButton icon="share">Share</PrimaryButton>

<!-- good -->
<PrimaryButton>
  <ShareIcon />
  Share
</PrimaryButton>
```

Techniques like compound components or scoped slots therefore try to achive more flexibility while reducing simplicity.

### Consistent and simple props

Avoid using boolean props and instead use `String` props. It can be difficult sometimes for a consumer to know which combination of props are valid. 

Is this a legal state?

```html
<Button primary secondary>Click me</Button>
```

Try this instead:

```html
<Button variant="primary">
```

Try to use the same prop names consistently. Instead of passing a value in one component as `value` prop and in another as `date` prop, always use `value` consistently.

## Do your research first

Don't start from scratch and steal great ideas from others. I want to encourage you to check out existing API designs, used abstractions, maybe even some implementation details.
This will save you some work and time when implementing your own component. Or even better you find a component you can just use instead of building your own.

A great place to start looking for high quality components is [Awesome Vue.js](https://github.com/vuejs/awesome-vue).

## Break up components into smaller pieces

Smaller components are better, because they are easier to understand and maintain, they encourage usability and they are the building blocks for more advanced composition techniques.

The level of abstraction in a single component should be consistent. Your component should either reuse other components or only deal with native DOM elements, like div, span, etc.

But don't go overboard. Splitting up components also introduces more complexity as for example state management. Only split up components if the reusability or testing would be complicated or 
the performance would suffer. When refactoring your components, try to follow the [Rule of three](https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming)) and only refactor when similar code
was used three times.

## Follow the shape of your data

You might have noticed that your components follow the shape of your data. Specifically, if your backend gives you some JSON data, ideally you want to have a component to render that data.
This is in contrast to building generic CSS components, as for example the [Bootstrap Card Component](https://getbootstrap.com/docs/4.0/components/card/). Our Vue.js components should be build to render the data,
so it should be for example a user card component instead. 

## Use composition patterns only when needed

The patterns we discussed in this book are not always the right tool for the job. Typically, when learning patterns I tend to overuse these patterns because I'm exciting to try out new techniques. 
But more often this produces too complex component. Don't start with such advanced patterns. Build your component first and then review if a pattern would be helpful to simplify things for you.

## Accessibility

Accessibility should not be an afterthought. There, I said it! I have to admit that I've only recently started embracing accessibility and I still got a lot to learn.

Fortunately, there are some good resources online as for example [Inclusive Components](https://inclusive-components.design/) and the [The A11 Project](https://a11yproject.com/).

## Easy styling

Once your components is used in several projects, consumers want to change the styling of your component and possibly in ways you haven't even imagined! Make sure that consumers 
can easily style and change the layout of your component without resorting to CSS overrides.

Using headless or renderless components make this simple, because these shift the rendering to the consumer of the component.

Another strategy could be to use a CSS module system as for example BEM to render the component with some basic styling initially. And in the next step let consumers provide CSS classes they can easily style.

## Testing

It sucks when you add this cool new feature, without noticing its breaking another existing feature. Unit testing your components gives you a safety net and confidence to refactor your 
component without breaking features.

The official Vue cookbook has a section on [Unit Testing Vue Components](https://vuejs.org/v2/cookbook/unit-testing-vue-components.html).

## Documentation

I love when using a new component is as simple as copy and pasting two lines from the documentation. From there I usually start with a deeper exploration and configuration. But, if the first
steps are too complex, I immediatelly feel a resistance to even give this component a try.

Great documentation turns a good component into a great component!

Don't let documentation be an afterthought. Start with a [README](https://en.wikipedia.org/wiki/README) file and describe the problem the component solves and how to use it, before writing your first line of code. It's what it's called
README driven development. It's great to let you focus on the actual problem first and not get lost in implementation details right away.

## Summary

In this chapter we looked into general best practices for designing components and I hope I've given you some new ideas.