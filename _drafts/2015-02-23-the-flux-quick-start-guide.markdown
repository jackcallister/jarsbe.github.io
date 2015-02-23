---
layout: post
title: The Flux Quick Start Guide
---

>*If you're unfamiliar with React JS read [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html) before diving into Flux.*

>*This article will give you an overview of how to build JavaScript applications with the Flux pattern, particularly in conjunction with React JS. It can be read as is or followed along with the [starter kit](https://github.com/jarsbe/flux-starter-kit).*

# Primary Concepts

Flux contains four primary concepts, three if you discount **Actions**.

## Views

**Views** are React components. They're responsible for rendering interfaces and handling user events. Their data is obtained from **Stores**.

## Stores

**Stores** are the data management layer. They notify the **Views** of any change in data.

## Actions

**Actions** are objects which contain data and an **Action Type**. Server responses or user events create **Actions** which are passed to the **Dispatcher** and or **Web Utils**.

## Dispatcher

The application **Dispatcher** receives **Actions** which it passes to all **Stores**.

**Stores** register interest in particular **Actions Types** and when these arrive from the **Dispatcher** the **Store** will modify it's data as needed.

# Secondary Concepts

There's also some secondary concepts which are very useful to know, especially when dissecting the official examples.

## Web Utils

Web Utilities, or **Web Utils** for short, communicate with external API's.

# Constants

**Constants** keep common values contained in a single place. Usually this is done with a [key mirror](https://www.npmjs.com/package/keymirror).

## Action Types

**Action Types** are a list of the **Actions** which can be created. They are kept as an object of **Constants** for easy accessibility and consistency.

## Actions Creators

**Action Creators** are objects which build **Actions** and pass these to the **Dispatcher**. As a side-effect they act as an internal **Dispatcher** & **Web Utils** API.

---

Now we can begin building an example application with Flux.

I highly suggest following along with the the starter kit and typing out each line to achieve the best understanding.

We'll build a very simple commenting application which will touch on each primary Flux concept. It's extremely rudimentary but the point is to convey the Flux pattern, not build a complex application.

## Views

Well begin with **Views**. After getting the starter kit setup (instructions are on the repository) you'll find the following `app.js` file in the `src` directory.

```
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

This renders our **Views** to the DOM. We'll ignore the `Comments` **View** and focus on the `CommentForm`.

```
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

This `CommentForm` **View** requires a `CommentActionCreators` object which is (as the name suggests) an **Action Creator**.

On form submission `createComment` is called on the **Action Creator**. It's passed a `comment` object which simply contains the form text. Let's implement the **Action Creator**.

## Actions

Under the `actions` directory create and implement the following `comment-actions-creator.js` file.

```
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

The `createComment` function calls `handleAction` on the **Dispatcher** which is passed an **Action** containing an **Action Type** and the comment data.

Let's implement the **Dispatcher**.

## Dispatcher

Under the `dispatcher` directory create and implement the following `app-dispatcher.js` file.

```
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

Firstly the a Dispatcher function from the Flux library is required.

Next a Dispatcher instance is merged with an object that implements the `handleAction` function. *This use the `assign` [helper](https://github.com/sindresorhus/object-assign).*

The `handleAction` function accepts an **Action** object and  calls `dispatch` passing in the supplied **Action**. *The dispatch function is supplied via the `new Dispatcher()` object.*

The `dispatch` function sends the 'payload' object to nothing at this point. Objects need to register their interest with the **Dispatcher** to access the payload. Those objects are **Stores**.

## Stores + Views

Under the `stores` directory create and implement the following `comment-store.js` file.

```
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

There are two segments of code in this file. The creation of the **Store** and registering with the **Dispatcher**.

The **Store** is created by merging the `EventEmitter.prototype` object and a custom object.

The `EventEmitter.prototype` imbues the **Store** with the ability to subscribe to and emit events.

The custom object gives the **Store** public functions for subscribing and unsubscribing to `change` events. It also exposes a `getAll` function which returns the private `comments` data.

In the next section the **Store** registers a function with the **Dispatcher**. When the **Dispatcher** calls `dispatch` it will call each registered function, passing the given payload.

In the `CommentStore`, when an **Action** has an **Action Type** equal to `CREATE_COMMENT` the **Store** will push the **Action's** comment data into it's comment array. Next the **Store's** `emitChange` function is invoked.

Now we need a component to display our **Store's** comments and subscribe to changes.

Inside the `views` directory find the `comments.js` file. Amend it to match the following.

```
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

Most of this is familiar React code with the addition of requiring the `CommentStore`. There's also a `getStateFromStores` function which retrieves the comment data from the store and wraps this in an object. This is the state used within the component.

Within `getInitialState` the component calls `getStateFromStores` which retrieves the comments and sets them as the components state.

Within `componentDidMount` a component function is registered with the **Store's** `addChangeListener`. This function `onChange` calls `getStateFromStores` to update the component's state.

Finally within `componentWillUnmount` the change listener is removed from the **Store**.

# All together now

With all of these pieces in place this is the essence of the Flux pattern. Submitting a comment creates an Action which is passed to the Dispatcher. The Dispatcher sends that payload to all registered callbacks. The Store's callback is triggered, updating it's data and emitting a change event trigger all registered callbacks to fire. The comment lists onChange function is invoked which updates the components state. This causes a re-render and the UI is updated with the correct data.

# In practice

This is a contrived and small example, useful only for expressing the parts of the Flux pattern. However try adding another component which displays the number of comments in the list. It will subscribe to the Stores data exactly like the Comment List.

Using **Constants** is omitted as are **Web Utils**. This isn't necessary for a demo. Instead try that on your own after having a look at the Flux Chat demo. It will make much more sense after completing this tutorial.
