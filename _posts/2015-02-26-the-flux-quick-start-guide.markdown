---
layout: post
title: The Flux Quick Start Guide
---


<svg class="flux" width="89px" height="36px" viewBox="0 0 89 36" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <path d="M70.92,0.34 C67.466,0.34 64.249,1.344 61.535,3.069 L44.439,15.625 L35.428,9.008 C34.885,9.889 34.338,10.765 33.815,11.672 C33.689,11.89 33.572,12.111 33.448,12.329 L40.98,17.86 L25.175,29.466 C23.009,30.816 20.517,31.53 17.958,31.53 C10.421,31.53 4.288,25.398 4.288,17.86 C4.288,10.322 10.42,4.19 17.958,4.19 C20.517,4.19 23.01,4.903 25.175,6.254 L26.746,7.407 C27.395,6.285 28.068,5.189 28.747,4.101 L27.343,3.07 C24.63,1.344 21.412,0.341 17.958,0.341 C8.283,0.341 0.439,8.185 0.439,17.86 C0.439,27.535 8.282,35.379 17.958,35.379 C21.412,35.379 24.63,34.375 27.343,32.65 L44.439,20.095 L53.485,26.738 C54.039,25.83 54.592,24.919 55.139,23.97 C55.247,23.782 55.345,23.595 55.452,23.407 L47.899,17.86 L63.703,6.254 C65.869,4.904 68.362,4.19 70.92,4.19 C78.458,4.19 84.59,10.322 84.59,17.86 C84.59,25.398 78.458,31.53 70.92,31.53 C68.361,31.53 65.869,30.817 63.703,29.466 L62.155,28.329 C61.499,29.462 60.835,30.563 60.168,31.645 L61.536,32.649 C64.25,34.375 67.467,35.378 70.921,35.378 C80.596,35.378 88.44,27.534 88.44,17.859 C88.439,8.184 80.596,0.34 70.92,0.34 L70.92,0.34 Z" fill="#44B74A">
  </path>
</svg>

>*This article will give you an overview of how to build JavaScript applications with the Flux pattern. It's the minimal amount of material to get you familiar with the core Flux concepts. You should follow along with the accompanying <a href="https://github.com/jarsbe/flux-starter-kit" target="_blank">starter kit</a>. You'll need a basic understanding of React and preferably some experience building components. Read the [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html) if you're unfamiliar with React.*

---

## Concepts

Flux is an architectural pattern for implementing user interfaces. It has three primary concepts; **Views**, **Stores** and the **Dispatcher**. There are also several secondary concepts; **Actions**, **Action Types**, **Action Creators** and **Web Utils**.

Take your time reading the following definitions then follow the tutorial. Read the definitions again and you'll be able to start using the Flux pattern in your own applications.

### Primary Concepts

**Views** are React components. They're responsible for rendering interfaces and handling user events. Their data is obtained from the Stores.

**Stores** manage data. A single Store manages data for a single domain. When a Store changes its data, it notifies the Views.

The **Dispatcher** receives new data and passes it to the Stores. Stores update their data (when applicable) and inform the Views.

### Secondary Concepts

**Actions** are the objects passed to the Dispatcher. They contain the new data and an Action Type.

**Action Types** are the Actions which the system can create. Stores only update their data when Actions with particular Action Types occur.

**Action Creators** are the objects which build Actions and send them to the Dispatcher or Web Utils.

**Web Utils** are objects for communicating with external API's. For example, an Action Creator may invoke requesting new data from the server.

---

There's quite a lot to absorb at once. I highly suggest following along with the the <a href="https://github.com/jarsbe/flux-starter-kit" target="_blank">starter kit</a> and typing out each line to achieve the best comprehension.

*Disclaimer: Use of constants and Web Utils is omitted and the Dispatcher has been simplified compared to most other guides. This makes understanding Flux simpler and once you've grokked the pattern reading the [official examples](https://github.com/facebook/flux/tree/master/examples) will fill in these secondary concepts.*

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

On form submission the `createComment` function is passed a `comment` object constructed from the textarea's value. Let's build this Action Creator to accept the comment.

---

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

The `createComment` function creates an Action which contains an Action Type (`CREATE_COMMENT`) and comment data. It calls `handleAction` on the Dispatcher and passes along the Action.

Let's build the Dispatcher to accept Actions and send them to all Stores.

---

## Dispatcher

Under the `dispatcher` directory, create and implement the following `app-dispatcher.js` file.

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

A new Dispatcher instance (from the Flux library) is merged with an object that implements the `handleAction` function. *This uses an* `assign` *[helper function](https://github.com/sindresorhus/object-assign) which merges objects.*

The `handleAction` function calls `dispatch` passing in the supplied Action. *The* `dispatch`* function is implemented via the* `new Dispatcher()` *object.*

The `dispatch` function sends its argument to nothing at this point. Stores need to register interest with the Dispatcher first.

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

There are two segments of code; Store creation and Store registration (with the Dispatcher).

The Store is created by merging an `EventEmitter.prototype` object and a custom object, similar to the Dispatcher creation. The `EventEmitter.prototype` imbues the Store with the ability to subscribe to and emit events.

The custom object defines public functions for subscribing and unsubscribing to `change` events. It also defines a `getAll` function which returns the `comments` data.

Next, the Store registers a function with the Dispatcher. When the Dispatcher calls `dispatch` it passes its argument to each registered function.

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
