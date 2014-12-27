---
layout: post
title: The React Quick Start Guide
---

<script src="http://static.jsbin.com/js/embed.js"></script>

<div class="atom">
  <div class="oval oval-forward"></div>
  <div class="oval oval-backward"></div>
  <div class="oval oval-horizontal"></div>
  <div class="circle"></div>
</div>

>*This article will give you a high level overview of how to build user interfaces in React JS. You'll learn just enough to get started. Grab this [boilerplate](https://github.com/jarsbe/react-quick-start-guide) to follow along, the instructions are in the repository.*

---

## Concepts

React has quite a small API. This makes it fun to use, easy to learn, and simple to understand. However, being simple does not mean all is familiar. There are some concepts to understand before you can get started.

**React elements** are JavaScript objects which represent HTML elements. They do not exist in the browser. React elements represent browser elements such as an `h1`, `div` or `section`.

**Components** are developer created React elements. Think of concepts such as a `NavBar`, `LikeButton` or `ImageUploader`. They are cohesive parts of the user interface which contain both the structure and functionality.

**JSX** is a technique for creating React elements and components. For example `<h1>Hello</h1>` is a React element written in JSX. The same React element can be written as JavaScript with `React.DOM.h1(null, 'Hello');`. JSX is cognitively less effort to read and write. It's transformed into JavaScript before running in the browser.

**The Virtual DOM** is a JavaScript tree of React elements and components, it exists only in memory. React renders the virtual DOM to the browser. React then observes the virtual DOM for changes and automatically makes those changes to the browser DOM.

With an understanding of these concepts we can move on to using React. We will build a series of user interfaces, each adding a layer of functionality on the previous. We are going to build a photo stream similar to instagram. Example applications don't get much better than this!

---

## Rendering

The first order of business is rendering a virtual element (a React element or component). Remember, since a virtual element exists only in JavaScript memory we must explicitly tell React to render it to the browser DOM.

``` js
React.render(<img src='http://tinyurl.com/lkevsb9' />, document.body);
```

The `render` function accepts two arguments; a virtual element and a DOM node. The virtual element is then rendered into the given DOM node.

---

## Components

Components are custom React elements. They can be extended with unique functionality and structure.

``` js
var Photo = React.createClass({

  render: function() {
    return <img src='http://tinyurl.com/lkevsb9' />
  }
});

React.render(<Photo />, document.body);
```

The `createClass` accepts an object which must implement a `render` function. In this example `render` returns a React image element.

The `Photo` component is constructed as a React element `<Photo />` and rendered to the document body. The JSX `<Photo />` is equivilent to `React.createElement(Photo, {})`.

This component is ready to be extended with custom functionality and structure.

---

## Props

Props can be thought of as a component's options. They are given as arguments when constructing a component, with JSX this appears very exactly like HTML attributes.

``` js
var Photo = React.createClass({

  render: function() {
    return (
      <div className='photo'>
        <img src={this.props.imageURL} />
        <span>{this.props.caption}</span>
      </div>
    )
  }
});

React.render(<Photo imageURL='http://tinyurl.com/lkevsb9' caption='New York!' />, document.body);
```

In the above example two props are passed to the component; `imageURL` and `caption`. The component uses it's `imageURL` prop as the `src` option for the React image element. The `caption` prop is used as plain text within the React span element.

It's worth remembering that a component should never change it's props, they are immutable. If a component has data that is mutable we use the state object.

---

## State

State is an object internal to a component which holds data that can change over time. Here's an example using the `Photo` component.

``` js
var Photo = React.createClass({

  toggleLike: function() {
    this.setState({
      liked: !this.state.liked
    });
  },

  getInitialState: function() {
    return {
      liked: false
    }
  },

  render: function() {
    var buttonClass = this.state.liked ? 'active' : '';

    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={this.toggleLike} className={buttonClass}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
});

React.render(<Photo src='http://tinyurl.com/lkevsb9' caption='New York!'/>, document.body);
```

There are two new functions and extra mark up in the render function.

React calls the `getInitialState` function when the component is initially ready for use. In this example it returns an object which contains a key, like, set to false. This object becomes the components state.

The second function `toggleLike` calls `setState` on the component. It simply toggles the value of like between true and false.

Within render `buttonClass` is assign either active or nothing depending on the like state. This variable is used as a class name on the React button element. This element also has an on click event handler set to the `toggleLike` function.

When the component is rendered to the browser it waits for user interaction. If a user clicks the button the `toggleLike` function is called and the button class name changes.

When a components state changes it's re-render to the virtual DOM. React then compares the new virtual DOM with the previous virtual DOM. Any differences between the two cause mutations to be executed on the browser DOM. This ensures the browser DOM is always syncronized with the virtual

---

## Composition

Building React applications and components requires composition. This means is combining smaller components together to form a larger whole.

``` js
var Photo = React.createClass({

  toggleLike: function() {
    this.setState({
      liked: !this.state.liked
    });
  },

  getInitialState: function() {
    return {
      liked: false
    }
  },

  render: function() {
    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={this.toggleLike}
                  className={this.state.liked ? 'active' : ''}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
});

var PhotoGallery = React.createClass({

  render: function() {
    var photos = this.props.data.map(function(photo) {
      return <Photo src={photo.src} caption={photo.caption} />
    });

    return (
      <div className='photo-gallery'>
        {photos}
      </div>
    )
  }
});

var data = [{
  src: 'http://tinyurl.com/lkevsb9',
  caption: 'New York!'
},
{
  src: 'http://tinyurl.com/mxkwh56',
  caption: 'Cows'
},
{
  src: 'http://tinyurl.com/nc7jv28',
  caption: 'Scooters'
}];

React.render(<PhotoGallery data={data} />, document.body);
```

Explain what is going on here.

## Conclusion

A bit of text explaining where to go from here.

This guide doesn't go into detail about local environment setup. Visit the [React docs](http://facebook.github.io/react/docs/getting-started.html) or look at my [boilerplate](https://github.com/jarsbe/react-webpack-boilerplate) to get started on your own.
