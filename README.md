# An interface component to apply React.js CSS transitions on initial render

`ReactCSSTransitionGroup` is an easy way to perform CSS transitions and animations when a React component enters or leaves the DOM.

When a new item is added to `ReactCSSTransitionGroup` it will get the `.transitionName-enter` CSS class and the `.transitionName-enter-active` CSS class added in the next tick. Subsequently, when an item is removed, it will receive the `.transitionName-leave` and `.transitionName-active` CSS classes.

This allows you to apply whatever CSS transitions you want to elements as they enter and leave the DOM.

## The problem

Recently while using the `ReactCSSTransitionGroup` add-on for React.js, I ran into the issue of not being able to apply the transition to the children element on the initial render.

### How the ReactCSSTransition component works by default

The reason this happens is because on intital render, both the `ReactCSSTransitionGroup` and the child elements appear in the DOM at the same time. The `ReactCSSTransitionGroup` will only apply the appropriate animation classes to any children elements which enter or leave the DOM after it has been rendered.

To illustrate what I mean, here is the example from the React.js documentation [implemented in CodePen](http://codepen.io/john0514/pen/OPxKVp).

The components inside the transition group will transition in and out of the page when added or removed as defined by the corresponding css rules, but they do not transition in on the initial render.

### A work-around to achieve the desired effect

One way around this is to delay the rendering of child components until after the component has mounted as outlined in [this StackOverflow answer](http://stackoverflow.com/questions/27385184/react-component-and-csstransitiongroup). This is a pretty simple work-around to implement, and requires adding the following code to the component which renders the `ReactCSSTransitionGroup` component.

	setInitialState: function() {
		return { mounted: false };
	},
	componentDidMount: function() {
		this.setState({ mounted: true });
	},
	render: function() {
		var child;
		if(this.state.mounted){
			child = (<div> ... </div>);
		}
		return (
			<ReactTransitionGroup transitionName="example">
				{child}
			</ReactTransitionGroup>
		);
	}

## How can this solution be improved?

The conditional re-render after mounting is a fine work around, but if I want to use this effect in multiple components then I'm going to be repeating it often. Furthermore, I don't particularly like the idea polluting the component state with an otherwise needless property.

Ideally I'd like a solution which doesn't affect my component state, and doesn't result in repeated code.

### Creating an interface component for ReactCSSTransitionGroup

To acheive this, I created an interface component for the `ReactCSSTransitionGroup` component. It's usage is identical to the `ReactCSSTransitionGroup`, however it has an additional prop called `transitionAppear` which takes a boolean value. This prop will handle the conditional rendering based on mounted state, then simply wraps the elements you want to animate with the regular `ReactCSSTransitionGroup` addon component.

	var ReactCSSTransitionGroup = React.addons.CSSTransitionGroup;

	var ReactCSSTransitionGroupAppear = React.createClass({

		propTypes: {
			transitionName: React.PropTypes.string.isRequired,
			transitionEnter: React.PropTypes.bool,
			transitionLeave: React.PropTypes.bool,
			transitionAppear: React.PropTypes.bool
		},

		getInitialState: function() {
			return {mounted: false}
		},

		getDefaultProps: function() {
			return {
				transitionEnter: true,
				transitionLeave: true,
				transitionAppear: true
			};
		},

		componentDidMount: function() {
			this.setState({ mounted: true });
		},

		render: function (){

			var children;

			if(!this.props.transitionAppear){
				children = this.props.children;
			}
			else{
				if(this.state.mounted){
					children = this.props.children;
				}
			}

			return(
				<ReactCSSTransitionGroup
					transitionName={this.props.transitionName}
					transitionEnter={this.props.transitionEnter}
					transitionLeave={this.props.transitionLeave}
				>
					{children}
				</ReactCSSTransitionGroup >
			)
		}
	})

To use this component, I simply include it in my app with the rest of my components and use it in place of the `ReactCSSTransitionGroup` add-on. [Here is a codePen](http://codepen.io/john0514/pen/jEGgap) using it in place of the ReactCSSTransitionGroup from the example in the React.js documentation.

## Conclusion

While this still obviously isn't an ideal solution, I think it does provide a cleaner implementation of the most common work-around.

It seems like the ability to apply a transition when the component first appears should be available in the `ReactCSSTransitionGroup` component soon. There is a [pull request](https://github.com/facebook/react/pull/2512) on Facebook's React.js GitHub repo which implements the functionality and it was merged in to the master branch last November. It has yet to make it in to a stable release, but hopefully it's not too far away.
