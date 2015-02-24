---
layout: post
title: The Flux Quick Start Guide
---

>*This article will give you an overview of how to build JavaScript applications with the Flux pattern, particularly in conjunction with React JS. This won't be a fully fledged application. There's a minimal amount of material to get your familiar with the core Flux concepts. It can be read as is or followed along with the [starter kit](https://github.com/jarsbe/flux-starter-kit).*

>*If you're unfamiliar with React JS you should read [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html) first. You'll need a basic understanding of React and preferably some experience building your own components.*

# Concepts

Flux simplifies data management within your application. It's unfamiliar at first but quickly becomes simple to understand and reason about.

Flux has three primary concepts; **Views**, **Stores** and the **Dispatcher**. There's also **Actions** which are objects sent throughout the **Views**, **Stores** and **Dispatcher**.

Take your time and read the following definitions twice or even three times. Follow the tutorial, then read the definitions again and you'll see how the concepts fit together.

## Views

**Views** are React components. They're responsible for rendering interfaces and handling user events. Their data is obtained from the **Stores**.

## Stores

**Stores** manage application data. A single **Store** manages data for a single domain. When a **Store's** data changes it notifies **Views** using that data.

## Dispatcher

The application **Dispatcher** receives **Actions**, created from user events or server events, which it passes to all **Stores**.

Each **Store** registers a function with the **Dispatcher**. This function indicates which **Actions Types** the **Store** is interested in.

When an **Action Type** is dispatched interested **Stores** will modify their data as needed.

## Actions

**Actions** are objects which contain data (from user or server events) and an **Action Type**. **Actions** are passed to the **Dispatcher** and or **Web Utils**.

## Action Types

**Action Types** are a list of the **Actions** which can be created. They are usually kept as an object of **Constants** for accessibility and consistency.

## Actions Creators

**Action Creators** are objects which build **Actions** and pass these to the **Dispatcher** and or **Web Utils**. As a side-effect they act as a **Dispatcher** & **Web Utils** API.

## Web Utils

Web Utilities, or **Web Utils** for short, are objects containing functions to communicate with external API's.

# Constants

**Constants** keep common values contained in a single place.  This is usually done with a [key mirror](https://www.npmjs.com/package/keymirror) object.

---

With a brief understanding of these concepts we can now implement the Flux pattern. I highly suggest following along with the the starter kit and typing out each line to achieve the best understanding. We'll build a simple commenting application touching on each primary Flux concept.

*Disclaimer: **Constants** and **Web Utils** are omitted from this guide and the **Dispatcher** has been simplified. Doing so makes the cognitive barrier to understanding Flux much lower. Once you've grokked the Flux pattern reading the [official examples](link) will fill in those aspects.*

## Views

After getting the [starter kit](https://github.com/jarsbe/flux-starter-kit) setup (instructions are in the repository) you'll find the following `app.js` file in the `src` directory.

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

This renders our **Views** to the DOM. We'll ignore the `Comments` **View** and focus on implementing the `CommentForm`.

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

On form submission `createComment` is called on the **Action Creator**. It's passed a `comment` object which contains the form text. Let's build the **Action Creator**.

## Actions

Under the `actions` directory create and implement the following `comment-action-creators.js` file.

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

Let's build the **Dispatcher**.

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

Firstly a Dispatcher function is required from the Flux library.

Next a Dispatcher instance is merged with an object that implements the `handleAction` function. *This uses an `assign` [helper function](https://github.com/sindresorhus/object-assign) which merges objects.*

The `handleAction` function accepts an **Action** object and  calls `dispatch` passing in the supplied **Action**. *The dispatch function is implemented via the `new Dispatcher()` object.*

The `dispatch` function sends it's argument to nothing at this point. Objects need to register interest with the **Dispatcher** first. Those objects are **Stores**.

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

There are two segments of code in this file. **Store** creation and **Store** registration with the **Dispatcher**.

The **Store** is created by merging the `EventEmitter.prototype` object and a custom object. The `EventEmitter.prototype` imbues the **Store** with the ability to subscribe to and emit events.

The custom object defines public functions for subscribing and unsubscribing to `change` events. It also contains a `getAll` function which returns the `comments` data.

Next, the **Store** registers a function with the **Dispatcher**. When the **Dispatcher** calls `dispatch` it passes it's argument to each registered function.

In this case, when an **Action** is dispatched with an **Action Type** equal to `CREATE_COMMENT`, this **Store** will push the comment data into it's comment array and invoke the `emitChange` function.

---

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

Most of this is familiar React code with the addition of requiring the `CommentStore` and a few added extras.

Firstly the `getStateFromStores` function retrieves comment data from the **Store** and returns this as an object. This is set as the initial component state within `getInitialState`.

Next inside `componentDidMount` the component's `onChange` function is passed to the **Store's** `addChangeListener` function. When the **Store** now emits a `change` event the `onChange` function will fire which sets the component's state as the new **Store** data.

Finally `componentWillUnmount` removes the `onChange` function from **Store**.

# Conclusion

We've now touched on every core concept of the Flux pattern; **Views**, **Stores** and the **Dispatcher**.

- When a user submits a comment an **Action Creator** builds an **Action** and sends this to the **Dispatcher**.
- The **Dispatcher** sends this **Action** to the registered **Store** callback.
- The **Store** updates it's comments data and emits a change event.
- The **View** updates it's state from the **Store** and re-renders with new data.

This is the essence of Flux. Create **Actions** which are sent to the **Dispatcher** which are sent to all **Stores**. The **Stores** update their data and the **Views** update their state.
