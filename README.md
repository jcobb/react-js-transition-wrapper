# An interface component to apply React.js CSS transitions on initial render

It's usage is identical to the `ReactCSSTransitionGroup`, however it has an additional prop called `transitionAppear` which takes a boolean value. This prop will handle the conditional rendering based on mounted state, then simply wraps the elements you want to animate with the regular `ReactCSSTransitionGroup` addon component.

To use this component, simply include it in your app with the rest of your components and use it in place of the `ReactCSSTransitionGroup` add-on.

It seems like the ability to apply a transition when the component first appears should be available in the `ReactCSSTransitionGroup` component soon. There is a [pull request](https://github.com/facebook/react/pull/2512) on Facebook's React.js GitHub repo which implements the functionality and it was merged in to the master branch last November. It has yet to make it in to a stable release, but hopefully it's not too far away.
