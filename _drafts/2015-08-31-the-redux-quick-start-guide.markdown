---
layout: post
title: The Redux Quick Start Guide
---

<svg class="flux" width="89px" height="36px" viewBox="0 0 89 36" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M70.92,0.34 C67.466,0.34 64.249,1.344 61.535,3.069 L44.439,15.625 L35.428,9.008 C34.885,9.889 34.338,10.765 33.815,11.672 C33.689,11.89 33.572,12.111 33.448,12.329 L40.98,17.86 L25.175,29.466 C23.009,30.816 20.517,31.53 17.958,31.53 C10.421,31.53 4.288,25.398 4.288,17.86 C4.288,10.322 10.42,4.19 17.958,4.19 C20.517,4.19 23.01,4.903 25.175,6.254 L26.746,7.407 C27.395,6.285 28.068,5.189 28.747,4.101 L27.343,3.07 C24.63,1.344 21.412,0.341 17.958,0.341 C8.283,0.341 0.439,8.185 0.439,17.86 C0.439,27.535 8.282,35.379 17.958,35.379 C21.412,35.379 24.63,34.375 27.343,32.65 L44.439,20.095 L53.485,26.738 C54.039,25.83 54.592,24.919 55.139,23.97 C55.247,23.782 55.345,23.595 55.452,23.407 L47.899,17.86 L63.703,6.254 C65.869,4.904 68.362,4.19 70.92,4.19 C78.458,4.19 84.59,10.322 84.59,17.86 C84.59,25.398 78.458,31.53 70.92,31.53 C68.361,31.53 65.869,30.817 63.703,29.466 L62.155,28.329 C61.499,29.462 60.835,30.563 60.168,31.645 L61.536,32.649 C64.25,34.375 67.467,35.378 70.921,35.378 C80.596,35.378 88.44,27.534 88.44,17.859 C88.439,8.184 80.596,0.34 70.92,0.34 L70.92,0.34 Z" fill="#44B74A">
  </path>
</svg>

>*This article will get you up to speed on using Redux for state managment within a React application. You should follow along with the accompanying <a href="https://github.com/jarsbe/redux-starter-kit" target="_blank">starter kit</a>. React and Flux are good pre-requisits but not absolutely neccessary. Read the [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html) if you're unfamiliar with React and or [The Flux Quick Start Guide]() if you need an introduction to Flux*

---

## Concepts

Redux is a library which provides an architectural pattern for managing the data that's used within your user interface. It's an evolution of the Flux pattern and has some important deviations. There are several new concepts which have their own lexicon to digest. Redux introduces *reducers*, *selectors* and a singular *store* rather than multiple domain specific stores.

Take your time reading the following definitions then follow the tutorial. Read the definitions again and you'll quickly grok how to use Redux in your own applications.

### Concepts

The **Store** is a single object containing all of your application data, or **state**. Redux creates the **store** by calling all of your **reducers** when initialising.

**Reducers** are functions which operate on and return data to the **store**. Thay are similar to traditional Flux stores. They use **actions** to control what data modifications should be made. Redux takes the new data and creates an new **store**. The store is then consumed by the views via **selectors**.

**Selectors** are functions which retrieve data for your views from the **store**. They can also derived the data which your views **require**.

There's quite a lot abstract content to absorb here. Following the tutorial with the provided starter kit will help solidify your understanding.

## Tutorial

>* This tutorial is very simple and designed to expose you to the Redux way of building a UI. We will start by using Redux to get data to the correct components, then we will connect actions to allow user interaction to modify that data.

After getting the <a href="https://github.com/jarsbe/redux-starter-kit" target="_blank">starter kit</a> setup (instructions are in the repository) you'll find the following `client.jsx` file in the `src` directory.

``` js
import React from 'react';
import { createStore, combineReducers } from 'redux';
import { Provider } from 'react-redux';
import App from '../shared/components/app';
import * as reducers from '../shared/reducers/index';

const combinedReducers = combineReducers(reducers);
const store = createStore(combinedReducers);

function app() {
  return <App />;
}

document.addEventListener('DOMContentLoaded', () => {
  React.render(
    <Provider store={store}>
      {app}
    </Provider>,
    document.getElementById('app')
  );
});
```

This renders the full our application into the DOM. The `Provider` component wraps a function that returns the `App` component. The `Provider` accepts a store prop, which is the single **store** mentioned earlier. Redux creates the **store** using it's `createStore` function which accepts an object containing all of your **reducers**.

Since each **reducer** returns the data for their domain, Redux is able to build your single **store** object.

The `Provider` attatches the Redux store to the React context object. This makes the store available to all components down the view heirachy.

The **reducers** are the biggest deviation from traditional Flux patterns, let's look at how they work.

---

# Reducers

There is currently only one **reducer** in the application, within the `src/reducers` directory find the `meals.js` file.

``` js
const initialState = [{
  id: 1,
  name: 'Butter Chicken'
}, {
  id: 2,
  name: 'Tikka Misala'
}];

export default function meals(state = initialState, action) {
  return state;
}

```

This is just a function which takes in data or *state* and an action. Currently this function just returns the initial **state**. There are no **actions** being used to make modifications and return a new **state**. Not yet at least.

This is similar to a **store** in traditional Flux patterns but you can see it is much simpler. There is no registering with the Dispatcher nor managing event listeners and emitting change events. The **state** and **action** are passed in, modifications made and a new state is returned.

The application has a store containing the state which is returned from our reducers. Let's use this data in a view (component).

---

## Actions

Under the `actions` directory create and implement the following `comment-action-creators.js` file.

``` js
var AppDispatcher = require('../dispatcher/app-dispatcher');

module.exports = {

  createComment: function(comment) {
    var action = {
      actionType: "CREATE_COMMENT",
      comment: comment
    };

    AppDispatcher.dispatch(action);
  }
};
```

The `createComment` function builds an Action which contains an Action Type and comment data. This Action is passed to the Dispatcher's `dispatch` function.

Let's build the Dispatcher to accept Actions.

*Note: We could write this code in the View - communicating with the Dispatcher directly. However, it's best practice to use an Action Creator. It decouples our concerns and provides a single interface for the Dispatcher.*

---

## Dispatcher

Under the `dispatcher` directory, create and implement the following `app-dispatcher.js` file.

``` js
var Dispatcher = require('flux').Dispatcher;

module.exports = new Dispatcher();
```

A single Dispatcher from the Flux library provides the `dispatch` function. Received Actions are passed to all of the registered callbacks. These callbacks are provided from the Stores.

*Note: Since the Dispatcher implementation is hidden, here's a [link to the source](https://github.com/facebook/flux/blob/master/src/Dispatcher.js#L181).*

---

## Stores

Under the `stores` directory, create and implement the following `comment-store.js` file.

``` js
var AppDispatcher = require('../dispatcher/app-dispatcher');

var EventEmitter = require('events').EventEmitter;
var assign = require('object-assign');

var comments = [];

var CommentStore = assign({}, EventEmitter.prototype, {

  emitChange: function() {
    this.emit('change');
  },

  addChangeListener: function(callback) {
    this.on('change', callback);
  },

  removeChangeListener: function(callback) {
    this.removeListener('change', callback);
  },

  getAll: function() {
    return comments;
  }
});

AppDispatcher.register(function(action) {

  switch(action.actionType) {

    case "CREATE_COMMENT":
      comments.push(action.comment);
      CommentStore.emitChange();
      break;

    default:
  }
});

module.exports = CommentStore;
```

There are two segments of code; Store creation and Store registration (with the Dispatcher).

The Store is created by merging an `EventEmitter.prototype` object and a custom object, similar to the Dispatcher creation. The `EventEmitter.prototype` imbues the Store with the ability to subscribe to and emit events.

The custom object defines public functions for subscribing and unsubscribing to `change` events. It also defines a `getAll` function which returns the `comments` data.

Next, the Store registers a function with the Dispatcher. When the Dispatcher calls `dispatch` it passes its argument, which is an Action, to each registered callback function.

In this instance, when an Action is dispatched with an Action Type of `CREATE_COMMENT`, the `CommentStore` will push the data into its comment array and invoke the `emitChange` function.

---

Now we need a View to display the Store's comments and subscribe to changes.

Inside the `views` directory find the `comments.js` file. Amend it to match the following.

``` js
var React = require('react');

var CommentStore = require('../stores/comment-store');

function getStateFromStore() {
  return {
    comments: CommentStore.getAll()
  }
}

var Comments = React.createClass({

  onChange: function() {
    this.setState(getStateFromStore());
  },

  getInitialState: function() {
    return getStateFromStore();
  },

  componentDidMount: function() {
    CommentStore.addChangeListener(this.onChange);
  },

  componentWillUnmount: function() {
    CommentStore.removeChangeListener(this.onChange);
  },

  render: function() {
    var comments = this.state.comments.map(function(comment, index) {
      return (
        <div className='comment' key={'comment-' + index}>
          {comment.text}
        </div>
      )
    });

    return (
      <div className='comments'>
        {comments}
      </div>
    );
  },
});

module.exports = Comments;
```

Most of this is familiar React code with the addition of requiring the `CommentStore` and a few Store related extras.

The `getStateFromStores` function retrieves comment data from the Store. This is set as the initial component state within `getInitialState`.

Inside `componentDidMount` the `onChange` function is passed to the Store's `addChangeListener` function. When the Store emits a `change` event `onChange` will now fire which sets the component's state as the updated Store data.

Lastly `componentWillUnmount` removes the `onChange` function from Store.

---

## Conclusion

We now have a working Flux application and have touched on every core concept of the pattern; **Views**, **Stores** and the **Dispatcher**.

- When a user submits a comment, the View invokes an Action Creator.
- The Action Creator builds an Action and passes this to the Dispatcher.
- The Dispatcher sends the Action to the registered Store callback.
- The Store updates its comment's data and emits a change event.
- The View updates its state from the Store and re-renders.

This is the essence of Flux. The Dispatcher sends data to all Stores which update and notify their Views.

At this point Flux will still feel a little unfamiliar. I highly reccommend reading the [official documentation](https://facebook.github.io/flux/) and watching the [intro to Flux talk](https://www.youtube.com/watch?v=nYkdrAPrdcw&list=PLb0IAmt7-GS188xDYE-u1ShQmFFGbrk0v) to get a deeper understanding of the Flux architecture and to further ingrain the pattern. I also suggest now reading the [official examples](https://github.com/facebook/flux/tree/master/examples).

If I've made a mistake or it doesn't quite click for you, ping me on [twitter](http://twitter.com/jarsbe) or better yet make a [pull request](https://github.com/jarsbe/jarsbe.github.io). Also, feel free to make suggestions for improving this guide.
