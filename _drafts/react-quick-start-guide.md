---
layout: post
title: React quick start guide
---

*This article will give you a high level overview of how to build user interfaces with React. You will learn the core React concepts and gain enough technical knowledge to start building your own UI.*

## Introduction

Using React is quite easy. The API is quite small, which makes it simple to express how you want your UI to look and behave. However, being simple does not mean it's familiar. Before building anything we must understand these concepts.

**Components** represent an isolated parts of your user interface. They contain both the structure and functionality. For example `ImageUploader`, `Menu` or `LikeButton`.

**React elements** are the JavaScript representation of HTML elements. They are essentially behaviour-less component. For example `h1`, `div` or `section`.

**JSX** is commonly used to create React elements and components. It appears exactly like HTML but is converted to JavaScript before execution. For example `<h1>Hello</h1>` & `<ImageUploader/>` is the same as `React.DOM.h1` & `React.createElement(ImageUploader, {})`.

**The virtual DOM** is a JavaScript tree of React elements and components (virtual elements). It can be rendered to the browser's DOM via the function `React.render`. Any change to the virtual DOM is automatically reconciled against the browser DOM.

*React elements and components will be referenced as 'virtual elements' for cohesion and to differentiate them from real DOM elements.*

## Getting started

Now that you have an understanding of the core React concepts we can move on to building user interfaces. You will start with the most simple example and slowly and more complexity and usefulness to your UI. We are going to build a like button.

Here are the steps you'll take:

- Render a React Element
- Create and render a component
- Create and render a component with properties
- Create and render a component with state
- Create and render a component by composing other components

### Render a React Element

The first thing you'll want to do is render a virtual element into the DOM.

<a class="jsbin-embed" href="http://jsbin.com/yolahu/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>

React's `render` function takes two arguments, a virtual element and a DOM node. The `render` function takes the virtual element and does exactly what you'd think, render's it into the given DOM node.

### Create and render a component

You now need to learn to create a component and render it to the DOM.

<a class="jsbin-embed" href="http://jsbin.com/kinute/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>

React's `createClass` accepts an object which contains a single key, render. The render key is given a function which returns a React button element. `createClass` returns the new component. This new component is then rendered to the document body, just like you would with a React element.

Why create a component when rendering the React button element would suffice? The advantage a component has over a React element is that one can extend the functionality of a component. This will become more apparent through use.

### Create and render a component with properties

Properties, or props for short, are conceptually similar to HTML attributes. The are options for virtual elements. Internally the component can use the prop. A prop is treated as immutable, meaning the component never changes the props value.

<a class="jsbin-embed" href="http://jsbin.com/dufecu/1/embed?js,output">JS Bin</a><script src="http://static.jsbin.com/js/embed.js"></script>
