---
layout: post
title: The Flux Quick Start Guide
---

>*This article will give you an overview of how to build JavaScript applications with the Flux pattern. It's the minimal amount of material to get you familiar with the core Flux concepts. It can be read as is or, preferably, followed along with the <a href="https://github.com/jarsbe/flux-starter-kit" target="_blank">starter kit</a>. You'll need a basic understanding of React and preferably some experience building components. Checkout the [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html) first if you're unfamiliar with React.*

---

## Concepts

Flux has three primary concepts; **Views**, **Stores** and the **Dispatcher**. There are also several secondary concepts; **Actions**, **Action Types**, **Action Creators** and **Web Utils**.

Take your time and read the following definitions twice or even three times. Follow the tutorial, then read the definitions again and you'll see how the concepts fit together.

## Primary Concepts

**Views** are React components. They're responsible for rendering interfaces and handling user events. Their data is obtained from the Stores.

**Stores** manage application data. A single Store manages data for a single domain. When a Store's data changes it notifies the Views using that data.

The **Dispatcher** receives Actions, created from user events or server events, which it passes to all Stores. Stores modify their data depending on what Action occured.

## Secondary Concepts

**Actions** are objects which contain data (from user or server events) and an Action Type. They are like messages which get passed throughout the Views, Stores and Dispatcher.

**Action Types** are a list of Actions which can be created. They are usually kept as an object of constants for accessibility and consistency.

**Action Creators** are objects which build Actions and pass these to the Dispatcher. Action Creators are also used to call Web Util functions.

**Web Utils** are objects for communicating with external API's.

---

There's quite a lot to absorb at once. I highly suggest following along with the the <a href="https://github.com/jarsbe/flux-starter-kit" target="_blank">starter kit</a> and typing out each line to achieve the best understanding.

*Disclaimer: Constants and Web Utils are omitted and the Dispatcher is simplified compared to most examples. This makes understanding Flux simpler and once you've grokked the pattern reading the [official examples](https://github.com/facebook/flux/tree/master/examples) will fill in these secondary concepts.*

## Views

After getting the <a href="https://github.com/jarsbe/flux-starter-kit" target="_blank">starter kit</a> setup (instructions are in the repository) you'll find the following `app.js` file in the `src` directory.

``` js
var React = require('react');
var Comments = require('./views/comments');
var CommentForm = require('./views/comment-form');

var App = React.createClass({

  render: function() {
    return (
      <div>
        <Comments />
        <CommentForm />
      </div>
    );
  }
});

React.render(<App />, document.getElementById('app'));
```

This renders our Views to the DOM. Ignore the `Comments` View and focus on implementing the `CommentForm` .

``` js
var React = require('react');

var CommentActionCreators = require('../actions/comment-action-creators');

var CommentForm = React.createClass({

  onSubmit: function(e) {
    var textNode = this.refs.text.getDOMNode();
    var text = textNode.value;

    textNode.value = '';

    CommentActionCreators.createComment({
      text: text
    });
  },

  render: function() {
    return (
      <div className='comment-form'>
        <textarea ref='text' />
        <button onClick={this.onSubmit}>Submit</button>
      </div>
    );
  }
});

module.exports = CommentForm;
```

The `CommentForm` requires a `CommentActionCreators` object which is (as the name suggests) an Action Creator.

On form submission `createComment` is called and passed a `comment` object which contains the form text. Let's build this Action Creator.

## Actions

Under the `actions` directory create and implement the following `comment-action-creators.js` file.

``` js
var AppDispatcher = require('../dispatcher/app-dispatcher');

module.exports = {

  createComment: function(comment) {
    AppDispatcher.handleAction({
      type: "CREATE_COMMENT",
      comment: comment
    });
  }
};
```

The `createComment` function calls `handleAction` on the Dispatcher and passes an Action containing an Action Type and comment data.

Let's build the Dispatcher.

## Dispatcher

Under the `dispatcher` directory create and implement the following `app-dispatcher.js` file.

``` js
var Dispatcher = require('flux').Dispatcher;
var assign = require('object-assign');

var AppDispatcher = assign(new Dispatcher(), {

  handleAction: function(action) {
    this.dispatch({
      action: action
    });
  }
});

module.exports = AppDispatcher;
```

A new Dispatcher instance (from the Flux library) is merged with an object that implements the `handleAction` function. *This uses an `assign` [helper function](https://github.com/sindresorhus/object-assign) which merges objects.*

The `handleAction` function calls `dispatch` passing in the supplied Action. *The dispatch function is implemented via the `new Dispatcher()` object.*

The `dispatch` function sends it's argument to nothing at this point. Objects need to register interest with the Dispatcher first. Those objects are Stores.

## Stores

Under the `stores` directory create and implement the following `comment-store.js` file.

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

AppDispatcher.register(function(payload) {
  var action = payload.action;

  switch(action.type) {

    case "CREATE_COMMENT":
      comments.push(action.comment);
      CommentStore.emitChange();
      break;

    default:
  }
});

module.exports = CommentStore;
```

There are two segments of code in this file. Store creation and Store registration (with the Dispatcher).

The Store is created by merging an `EventEmitter.prototype` object and a custom object. The `EventEmitter.prototype` imbues the Store with the ability to subscribe to and emit events.

The custom object defines public functions for subscribing and unsubscribing to `change` events. It also defines a `getAll` function which returns the `comments` data.

Next, the Store registers a function with the Dispatcher. When the Dispatcher calls `dispatch` it passes it's argument to each registered function.

In this case, when an Action is dispatched with an Action Type equal to `CREATE_COMMENT`, the `CommentStore` will push the Actios comment data into it's comment array and invoke the `emitChange` function.

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

The `getStateFromStores` function retrieves comment data from the Store. This is called an set as the initial component state within `getInitialState`.

Inside `componentDidMount` the component's `onChange` function is passed to the Store's `addChangeListener` function. When the Store emits a `change` event the `onChange` function will fire which sets the component's state as the new Store data.

Finally `componentWillUnmount` removes the `onChange` function from Store.

---

## Conclusion

We now have a working application and have touched on every core concept of the Flux pattern; **Views**, **Stores** and the **Dispatcher**.

- When a user submits a comment the View invokes an Action Creator.
- The Action Creator builds an Action and passes this to the Dispatcher.
- The Dispatcher sends the Action to the registered Store callback
- The Store updates it's comments data and emits a change event.
- The View updates it's state from the Store and re-renders.

This is the essence of Flux. Create Actions which are sent to the Dispatcher which are sent to all Stores. The Stores update their data and the Views update their state.
