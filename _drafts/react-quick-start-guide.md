---
layout: post
title: The React JS quick start guide
---

<div class="atom">
  <div class="oval oval-forward"></div>
  <div class="oval oval-backward"></div>
  <div class="oval oval-horizontal"></div>
  <div class="circle"></div>
</div>

>*This article will give you a high level overview of how to build user interfaces in React JS. You will learn just enough to get started.*

---

React JS has quite a small API. This makes it fun to use, easy to learn, and simple to understand. However, being simple does not equal familiar. There are some concepts to understand before you can get started. Let's cover each as succinctly as possible.

**React elements** are JavaScript objects which represent HTML elements. They do not exist in the browser. React elements represent browser elements such as an `h1`, `div` or `section`.

**Components** are developer created React elements. Think of concepts such as a `NavBar`, `LikeButton` or `ImageUploader`. They are cohesive parts of the user interface which contain both structure and functionality.

**JSX** is a technique for creating React elements and components. For example `<h1>Hello</h1>` is a React element written in JSX. The same React element can be written as JavaScript with `React.DOM.h1`. It's transformed into JavaScript before running.

**The Virtual DOM** is a JavaScript tree of React elements and components. It doesn't exist in the browser's DOM, only in JavaScript memory. The Virtual DOM can be rendered to the browser and any changes to it are automatically updated in browser's DOM.

With a vaugue understanding of these concepts you can now move on to using React JS. You will build a series of user interfaces, each adding more complexity and functionality on the previous. You are going to build an interactive image gallery where one can learn the names of dinosaurs. Example applications don't get better than that!

*This guide utilizes JS Bin to convey React concepts, it does not go into detail about getting your local environment set up. Visit the [React JS docs](http://facebook.github.io/react/docs/getting-started.html) or use this [boilerplate](https://github.com/jarsbe/react-webpack-boilerplate) to get started after this reading this guide.*

The first order of business is rendering a virtual element (a React element or component). Since a virtual element only exists in JavaScript memory you must tell React JS to render it into the browser's DOM.

<a class="jsbin-embed" href="http://jsbin.com/fubariyuhe/3/embed?js,output">React JS quick start guide</a><script src="http://static.jsbin.com/js/embed.js"></script>

The `render` function accepts two arguments; a virtual element and a DOM node. The virtual element is then rendered into the browser's DOM node.

Try replacing the `img` with any another React element such as an H1. Remember you're creating a virtual element in JSX and rendering it to the page. This could be achieved with just HTML but you will see why React uses a virtual DOM as you move onto building and using components.

Next, you need create and render a component. Recall that a component is essentially a developer created React element. This allows you to embue the component with functionality beyond that of regular elements.

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
