---
layout: post
title: React JS quick start guide
---

*This article will give you a high level overview of how to build user interfaces in React JS. You will learn just enough to start building your own UI.*

---

## Introduction

Learning React JS is quite easy. The API is very small which makes it quick to learn, fun and simple. However, being simple does not equal familiar. There are some concepts to understand before you can get started. Let's cover each as succinctly as possible.

### React elements

React elements are JavaScript objects which represent HTML elements. They do not exist in the browser. React elements represent browser elements such as an `h1`, `div` or `section`.

### Components

Components are developer created React elements. Think of concepts such as a `NavBar`, `LikeButton` or `ImageUploader`. They are cohesive parts of the user interface which contain both structure and functionality.

### JSX

JSX is a technique for creating React elements and components. For example `<h1>Hello</h1>` is a React element written in JSX. The same React element can be written as JavaScript with `React.DOM.h1`. It's transformed into JavaScript before running.

### The Virtual DOM

The Virtual DOM is a JavaScript tree of React elements and components. It doesn't exist in the browser's DOM, only in JavaScript memory. The Virtual DOM can be rendered to the browser and any changes to it are automatically updated in browser's DOM.

---

## Rendering

*This guide utilizes JS Bin - feel free to edit and explore the examples as they are explained.*

To build a user interace with React JS you need to learn how to render a virtual element (a React element or component).

```
React.render(<button>Like</button>, document.body);
```

<!-- <a class="jsbin-embed" href="http://jsbin.com/yolahu/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script> -->

The `render` function takes two arguments, a virtual element and a DOM node. The virtual element is rendered into the browser's DOM node.

---

## Components

You now need to learn how to create and render a component.

```
var LikeButton = React.createClass({

  render: function(){
    return <button>Like</button>
  }
});

React.render(<LikeButton />, document.body);
```

<!-- <a class="jsbin-embed" href="http://jsbin.com/kinute/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script> -->

React's `createClass` accepts an object which contains a single key, render. The render key is given a function which returns a React button element. `createClass` returns the new component. This new component is then rendered to the document body, just like you would with a React element.

Why create a component when rendering the React button element would suffice? The advantage a component has over a React element is that one can extend the functionality of a component. This will become more apparent through use.

### Properties

Properties, or props for short, are conceptually similar to HTML attributes. The are options for virtual elements. Internally the component can use the prop. A prop is treated as immutable, meaning the component never changes the props value.

<!-- <a class="jsbin-embed" href="http://jsbin.com/dufecu/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script> -->
