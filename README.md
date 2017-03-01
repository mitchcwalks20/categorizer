# Categorizer
<img src="https://raw.githubusercontent.com/DevMountain/categorizer/master/readme_assets/completed.png" />

### Project Summary

During this project we'll be building a web application that allows for easily categorizing information in radar charts. Users will be able to keep track of multiple categories, each category having multiple data sets. To keep track of this data and pass it to the correct components we'll make heavy use of Redux and React Redux.


### Setup

Get started with the usual steps: 

* Fork and clone this repository
* `cd` into the project directory
* `npm i` to download the included dependencies
* `npm test` to start the test suite
* `npm start` to spin up the development server

### Step 1

**Summary**

The first step will be focused on the initial setup required to make a Redux application. We will install the required dependencies, create a reducer and Redux store, and connect the application to Redux.

**Instructions**

* Install Redux and React Redux
* Create `src/ducks/chart.js`
* Write an initial state and reducer inside of `src/ducks/chart.js`
* Create `src/store.js`
* Create the Redux store in `src/store.js`
* Connect the application to Redux in `src/index.js`
* Connect the `App` component definition to Redux

**Detailed Instructions**

Start by installing the following dependencies

* [`redux`](http://redux.js.org/) - A state container for JavaScript applications. This library allows us to easily store and access information  from across an entire application.
* [`react-redux`](https://github.com/reactjs/react-redux) - The official bindings to seamlessly connect a React application to Redux.

As those install, create a new directory inside of `src` named `ducks`. This is the directory where our reducer will live. Inside of `src/ducks` create a file `chart.js`. `chart.js` will hold a reducer, action types, action creators, and the reducer's initial state.

Open up `src/ducks/chart.js` and start by creating an `initialState` variable. `initialState` should be an object with two properties:

* `activeChartIndex` - This is where we will store the index of the chart that the user has chosen to display. It should default to `0`
* `charts` - This will be an array of objects containing the data necessary to create the charts. We'll have it default to an array containing an example chart object that looks like this:

```javascript
{
  // Labels corresponding to the corners of the chart.
  labels: [ "Red", "Blue", "Yellow", "Green", "Purple", "Orange" ]
  // The name of the chart
, name: "Example Chart"
  // The data required for rendering values to the chart
, datasets: [
		{
			  // The name of the dataset
			  label: "My First dataset"
			  // Each of these numbers corresponds to one of the labels above,
			  // based on index
			, data: [65, 59, 90, 81, 56, 55, 40]
		}
		, {
			  label: "My Second dataset"
			, data: [28, 48, 40, 19, 96, 27, 100]
		}
	]
}
```

Once the `initialState` is created we can create our reducer. Create and export by default a function named `chart` which takes two parameters

* `state` - This will be an object representation of our application's current state. It should default to `initialState`.
* `action` - An object containing information about what has occurred, and any data necessary to perform a state change.

Add a `switch` statement to the `chart` function that checks `action.type`. Later it will check for specific types, but for now just give it a `default` case that returns `state`.

Now that the bones of the reducer are in place, we can create the application's Redux store. Create a new file in `src` named `store.js`. Inside of `src/store.js` import `createStore` from Redux and `chart` from `src/chart.js`. Create the Redux store by invoking `createStore` and passing in `chart`, export the store by default.

What is Redux doing behind the scenes here? When Redux first initializes it will call the `chart` reducer function passing `undefined` as the first argument and a dummy action as the second argument. Our function will return the default state value of `initialState`, giving Redux an initial value to work with.

Now to connect the React application to Redux. In `src/index.js` import `Provider` from React Redux and `store` from `src/store.js`. Wrap the `App` component in the `Provider` component, passing `store` as a prop to `Provider`.  `Provider` is simply a wrapper component that gives the rest of the application access to the `store`.

Finally, in `src/components/App.js` import `connect` from React Redux. Underneath the component definition create a function named ``mapStateToProps` that takes a single parameter `state`. This function is how we tell Redux which pieces of state a component is interested in as well as the format they are passed in. We want all of the state data, but we also want to make life a little easier on ourselves, so `mapStateToProps` will return an object with two properties:

* `activeChart` - Set equal to `charts[ state.activeChartIndex ]`
* `charts` - Set equal to `state.charts`

Note that we aren't simply returning `{ activeChartIndex: state.activeChartIndex, charts: state.charts }`, instead we are grabbing a reference to the actual active chart itself. Now we can access it easily throughout our components!

To finish connecting the `App` component definition we need to create a decorator by invoking `connect` and passing in `mapStateToProps`, then invoke the decorator passing in `App`. Export the decorated component by default. Decorators take some getting used to, so here's a reminder:

<details>

<summary>Decorator Example</summary>

```javascript
function mapStateToProps( state ) {
    return state;
}
const decorator = connect( mapStateToProps );
const decoratedComponent = decorator( App );
export default decoratedComponent;
```

This is usually shortened to

```javascript
function mapStateToProps( state ) {
    return state;
}
export default connect( mapStateToProps )( App );
```

___

</details>

That's it for step 1! Nothing appears to have changed, but we've laid the groundwork we'll build on over the next steps!

<details>

<summary>**Code Solution**</summary>

<details>

<summary>`src/ducks/chart.js`</summary>

```javascript
const initialState = {
	  activeChartIndex: 0
	, charts: [ {
		  labels: [ "Red", "Blue", "Yellow", "Green", "Purple", "Orange" ]
		, name: "Example Chart"
		, datasets: [
			{
				  label: "My First dataset"
				, data: [65, 59, 90, 81, 56, 55, 40]
			}
			, {
				  label: "My Second dataset"
				, data: [28, 48, 40, 19, 96, 27, 100]
			}
		]
	} ]
};

export default function chart( state = initialState, action ) {
	switch ( action.type ) {
		default: return state;
	}
}

```

</details>

<details>

<summary>`src/store.js`</summary>

```javascript
import { createStore } from "redux";

import chart from "./ducks/chart";

export default createStore( chart );
```

</details>

<details>

<summary>`src/index.js`</summary>

```javascript
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";

import "./index.css";

import store from "./store";

import App from "./components/App";

ReactDOM.render(
	<Provider store={ store }>
		<App />
	</Provider>,
	document.getElementById( 'root' )
);

```

</details>

</details>

### Step 2

**Summary**

In this step we will be connecting a component to Redux, creating our first action type/creator, and modifying the reducer to be able to handle the action.

**Instructions**

* Create a `CREATE_CHART` action type and corresponding action creator
* Modify the `chart` reducer to handle to handle adding a new chart

**Detailed Instructions**

This step will be in `src/ducks/chart.js`. At the top of the file create an action type `CREATE_CHART` and set it equal to `"CREATE_CHART"`. This action type is just a description of what happened used by the reducer to determine how to change state.

Underneath the `chart` reducer, create and export a function named `createChart`. `createChart` will take in two parameters:

* `labels` - An array of labels that the chart will have
* `name` - The name of the chart specified by the user

`createChart` should return an object with two properties:

* `chart` - An object containing the necessary chart data: `{ labels, name, datasets: [] }`
* `type` - The action type, in this case `CREATE_CHART`

With the action creator ready to go, we now need to update the reducer function itself to handle the action. Add a new `case` to the `switch` statement that checks the `action.type` against `CREATE_CHART` (put this above the default case, or it will never run!). This case should return a new state object where

* `charts` is an array of `action.chart` and all the past `charts` on state
* `activeChartIndex` is set to `0`, the index of the newly created chart.

Remember not to mutate state! You should be returning a brand new object based on the values from the previous object.

We'll hook this action up to the GUI in the next step, but for now you can test your reducer by calling it manually and examining the result. `chart( undefined, createChart( [ "foo", "bar", "baz" ], "test" ) );` should return something like this:

```javascript
{
  "activeChartIndex": 0,
  "charts": [
    {
      "labels": [
        "foo",
        "bar",
        "baz"
      ],
      "name": "test",
      "datasets": []
    },
    {
      "labels": [ /* labels */ ],
      "name": "Example Chart",
      "datasets": [
        {
          "label": "My First dataset",
          "data": [ /* numbers */ ]
        },
        {
          "label": "My Second dataset",
          "data": [ /* numbers */ ]
        }
      ]
    }
  ]
}
```

<details>

<summary>**Code Solution**</summary>

```javascript
// src/ducks/chart.js
const CREATE_CHART = "CREATE_CHART";

const initialState = {
	  activeChartIndex: 0
	, charts: [ {
		  labels: [ "Red", "Blue", "Yellow", "Green", "Purple", "Orange" ]
		, name: "Example Chart"
		, datasets: [
			{
				  label: "My First dataset"
				, data: [65, 59, 90, 81, 56, 55, 40]
			}
			, {
				  label: "My Second dataset"
				, data: [28, 48, 40, 19, 96, 27, 100]
			}
		]
	} ]
};

export default function chart( state = initialState, action ) {
	switch ( action.type ) {
		case CREATE_CHART:
			return {
				  activeChartIndex: 0
				, charts: [ action.chart, ...state.charts ]
			};
		default: return state;
	}
}

export function createChart( labels, name ) {
	return {
		  chart: { labels, name, datasets: [] }
		, type: CREATE_CHART
	}
}

```

</details>

### Step 3

**Summary**

In this step we'll implement the ability to create charts in the `NewChart` component.

**Instructions**

* Import and connect the `createChart` action creator in `App`
* Pass the `createChart` to the `NewChart` component
* Alter the `NewChart` component definition to allow for handling user input
* Use the `createChart` function to pass the user input to Redux

**Detailed Instructions**

We'll begin this step in `src/components/App.js`. Import `createChart` from `src/ducks/chart.js`. If we were to invoke `createChart` in our component right now, what would happen? Would Redux receive the action?

It wouldn't! `createChart` is just a function that returns an action object. To send the action to Redux we need to wrap it in Redux's [`dispatch`](http://redux.js.org/docs/api/Store.html#dispatch) function. Luckily React Redux's `connect` can do just that for us. As the second argument to `connect` (after `mapStateToProps`) pass an object containing the `createChart` function. This will place `createChart` on `App`'s props as well as wrapping it in `dispatch` for us.

<details>

<summary>The magic behind `connect` wrapping action creators</summary>

It may feel a little like magic, but the wrapping of action creators in dispatch is fairly simple! The actual source code will be different, but this is accomplishing the same thing.

```javascript
// Take in an object of action creators, i.e { createChart }
function wrapActionCreator( actionCreatorsObject ) {
	// A new object that will hold the wrapped action creators
	const wrappedActionCreators = {};
	// Iterate over each action creator in the object
	for ( let actionCreator in actionCreatorsObject ) {
		// Creating a new function to capture arguments to the action creator
		// such as "labels" and "name"
		wrappedActionCreators[ actionCreator ] = ( ...args ) => {
			// Create the action, passing in the captured arguments
			const action = actionCreatorsObject[ actionCreator ]( ...args );
			// Dispatch the action to Redux
			dispatch( action );
		}
	}
	return wrappedActionCreators;
}
```

___

</details>

All that is left to do in `App` is to pass `createChart` as a prop to the `NewChart` component.

Open up `src/components/NewChart/NewChart.js`. Get started by writing a `constructor` method (don't forget to `super( props );`!) where we'll create a `state` object with three properties:

* `labels` - A list of the labels submitted so far. Defaults to an empty array
* `name` - The text from the name input. Defaults to an empty string
* `newLabel` - The text from the new label input. Defaults to an empty string

Next up we'll need a `handleChange` method so we can accept user input. `handleChange` will take two arguments:

* `field` - The name of the field that is changing, i.e `"name"` or `"newLabel"`
* `event` - The DOM event triggering the change and carrying the new value

All this method needs to do is update the specified field on state with the value on the event. It will look something like this: `this.setState( { [ field ]: event.target.value } );`.  Before we attach this method to the JSX, let's `bind` in the constructor, because we have to handle changes from two different fields, we'll need to bind twice. It will look like this:

```javascript
constructor( props ) {
	super( props );

	this.state = {
		  labels: []
		, name: ""
		, newLabel: ""
	};

	this.handleNameChange = this.handleChange.bind( this, "name" );
	this.handleNewLabelChange = this.handleChange.bind( this, "newLabel" );
}
```

Now we can dive into the JSX to make use of what we have so far! At the top of `render` destructure `labels`, `name`, and `newLabel` from `this.state`. Both inputs will need two new props:

* `value` - set equal to `name` or `newLabel` respectively
* `onChange` - set equal to `handleNameChange` or `handleNewLabelChange` respectively

Next we need to add a way for users to save their labels, we'll do that by creating a new method `addLabel`. `addLabel` will take a single `event` parameter. What this method needs to do is call `event.preventDefault()`, add `this.state.newLabel` to `this.state.labels`, and reset `this.state.newLabel` to an empty string. It will look something like this:

```javascript
addLabel( event ) {
	// We need to prevent default because this will be attached to a form
	// element. Without this, the browser will reload!
	event.preventDefault();

	this.setState( {
		  labels: [ ...this.state.labels, this.state.newLabel ]
		, newLabel: ""
	} );
}
```

Bind `addLabel` in the `constructor` and then pass it to the `onSubmit` prop of the form containing the "Add Label" input. To let the user see what labels they have already added add the following code inside of the `[]` brackets in the `new-chart__labels` span - `{ labels.join( ", " ) }`. With these changes you should be able to enter labels and see them populate below as you hit enter.

Finally we need to send all this data to our reducer! To do this we'll need one more method - `submitChart` which won't take any parameters. Destructure `labels` and `name` from `this.state` so we can do a little bit of form validation. If `name` is an empty string or there are less than 3 labels we will just return early. Next we need to call `this.props.createChart` (our action creator passed down from app) passing in `labels` and `name`. Lastly, reset `this.state` to its initial value. Bind `submitChart` in the `constructor` and pass it to the `onClick` handler of the submit button. It will look like this:

```javascript
submitChart() {
	const { labels, name } = this.state;

	if ( !name || labels.length < 3 ) {
		return;
	}

	this.props.createChart( labels, name );

	this.setState( {
		  labels: []
		, name: ""
		, newLabel: ""
	} );
}
```

You're now able to send all the data necessary for creating a chart to the reducer! Unfortunately the chart isn't visible yet, but we'll cover that in the next step.

<details>

<summary>**Code Solution** </summary>

<details>

<summary>`src/components/App.js`</summary>

```jsx

```

</details>

<details>

<summary>`src/components/NewChart/NewChart.js`</summary>

```jsx

```

</details>

</details>

## Contributions

### Contributions

#### 
 
If you see a problem or a typo, please fork, make the necessary changes, and create a pull request so we can review your changes and merge them into the master repo and branch.

## Copyright

### Copyright

#### 

© DevMountain LLC, 2017. Unauthorized use and/or duplication of this material without express and written permission from DevMountain, LLC is strictly prohibited. Excerpts and links may be used, provided that full and clear credit is given to DevMountain with appropriate and specific direction to the original content.

<img src="https://devmounta.in/img/logowhiteblue.png" width="250">
