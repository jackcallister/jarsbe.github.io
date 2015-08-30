---
layout: post
title: 'The React Quick Start Guide: ES6 Edition'
---

<div class="atom">
  <div class="oval oval-forward"></div>
  <div class="oval oval-backward"></div>
  <div class="oval oval-horizontal"></div>
  <div class="circle"></div>
</div>

>*This article is the ES6 edition of the original [React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html). Babel, a JavaScript compiler, combined with React will enable you to write modern, understandable and maintainable user interface code. There's just enough to get yourself started and nothing more. Code along with this <a targe="_blank" href="https://github.com/jarsbe/react-starter-kit-es6">starter kit</a> (instructions in the repo) or just read along.*

---

## Concepts

React has quite a small API. This makes it fun to use, easy to learn, and simple to understand. However, being simple does not mean it's familiar. There are a few concepts to cover before getting started. Let's look at each in turn.

**React elements** are JavaScript objects which represent HTML elements. They do not exist in the browser. They represent browser elements such as an `h1`, `div` or `section`.

**Components** are developer created React elements. They're usually larger parts of the user interface which contain both the structure and functionality. Think of concepts such as a `NavBar`, `LikeButton` or `ImageUploader`.

**JSX** is a technique for creating React elements and components. For example `<h1>Hello</h1>` is a React element written in JSX. The same React element can be written as JavaScript with `React.DOM.h1(null, 'Hello');`. JSX is less effort to read and write and is transformed into JavaScript before running in the browser.

**The Virtual DOM** is a JavaScript tree of React elements and components. React renders the virtual DOM to the browser to make the user interface visible. React observes the virtual DOM for changes and automatically mutates browser DOM to match the virtual DOM.

With a small understanding of these concepts we can move on to using React. We'll build a series of user interfaces, each adding a layer of functionality on the previous. We'll build a photo stream similar to instagram - example applications don't get much better than this!

---

## Rendering

The first order of business is rendering a virtual element (a React element or component). Remember, since a virtual element exists only in JavaScript memory, we must explicitly tell React to render it to the browser DOM.

``` js
React.render(<img src='http://tinyurl.com/lkevsb9' />, document.getElementById('app'));
```

<a target="_blank" href="https://jsbin.com/mireve/edit?js,output">View JSBin</a>

The `render` function accepts two arguments; a virtual element and a real DOM node. React takes the virtual element and inserts it into the given DOM node. The image is now visible in the browser.

---

## Components

Components are the heart and soul of React. They are custom React elements. They are usually extended with unique functionality and structure.

``` js
class Photo extends React.Component {

  render() {
    return <img src='http://tinyurl.com/lkevsb9' />
  }
}

React.render(<Photo />, document.getElementById('app'));
```
<a target="_blank" href="https://jsbin.com/qanopi/4/edit?js,output">View JSBin</a>

A `Photo` class is defined as a subclass of the React Component class. It has a single `render` function which returns a React image element.

The `Photo` component is constructed, `<Photo />`, and rendered to the application div.

This component does nothing more than the previous React image element but it's ready to be extended with custom functionality and structure.

---

## Props

Props can be thought of as a component's options. They're given as arguments to a component and look exactly like HTML attributes.

``` js
class Photo extends React.Component {

  render() {
    return (
      <div className='photo'>
        <img src={this.props.src} />
        <span>{this.props.caption}</span>
      </div>
    )
  }
}

React.render(<Photo src='http://tinyurl.com/lkevsb9' caption='Hong Kong!' />, document.getElementById('app'));
```
<a target="_blank" href="https://jsbin.com/meqaqi/5/edit?js,output">View JSBin</a>

Inside the React `render` function, two props are passed to the `Photo` component; `imageURL` and `caption`.

Inside the component's `render` function the `imageURL` prop is used as the `src` for the React image element. The `caption` prop is also used as plain text within the React span element.

It's worth noting that a component should never change its props, they're immutable. If a component has data that's mutable, use the state object.

---

## State

The state object is internal to a component. It holds data which can change over time.

``` js
class Photo extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      liked: false
    }
  }

  toggleLiked() {
    this.setState({
      liked: !this.state.liked
    });
  }

  render() {
    const buttonClass = this.state.liked ? 'active' : '';

    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={(e) => {this.toggleLiked(e)}} className={buttonClass}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
}

React.render(<Photo src='http://tinyurl.com/lkevsb9' caption='Hong Kong!' />, document.getElementById('app'));
```
<a target="_blank" href="https://jsbin.com/vuhize/3/edit?js,output">View JSBin</a>

Having state in a component introduces a bit more complexity.

The component class has a `constructor` function which is called when the component is initialised. During this initialising the component state is set. In this case `liked` is set to false.

*Note: The super function must be called (with the props argument) for React to prepare the class for rendering.*

The component has another new function `toggleLiked`. This function calls `setState` on the component which toggles the `liked` value.

Within the component's render function a variable `buttonClass` is assigned either 'active' or nothing - depending on the `liked` state.

`buttonClass` is used as a class name on the React button element. The button also has an `onClick` event handler set to the `toggleLiked` function. The `onClick` handler uses an ES6 arrow syntax which is shorthand for `function(e) { return this.toggleLiked(e)}`.

Here's what happens when the component is rendered to the browser DOM:

- When the component's button is clicked, `toggleLiked` is called
- The `liked` state is changed
- React re-renders the component to the virtual DOM
- The new virtual DOM is compared with the previous virtual DOM
- React isolates what has changed and updates the browser DOM

In this case, React will change the class name on the button.

---

## Composition

Composition means combining smaller components to form a larger whole. For example the `Photo` component could be used inside a `PhotoGallery` component, like so:

``` js
class Photo extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      liked: false
    }
  }

  toggleLiked() {
    this.setState({
      liked: !this.state.liked
    });
  }

  render() {

    const buttonClass = this.state.liked ? 'active' : '';

    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={(e) => {this.toggleLiked(e)}} className={buttonClass}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
}

class PhotoGallery extends React.Component {

  render() {

    const photos = this.props.photos.map((photo) => {
      return <Photo src={photo.url} caption={photo.caption} />
    });

    return (
      <div className='photo-gallery'>
        {photos}
      </div>
    );
  }
};

const data = [
  {
    url: 'http://tinyurl.com/lkevsb9',
    caption: 'Hong Kong!'
  },
  {
    url: 'http://tinyurl.com/mxkwh56',
    caption: 'Cows'
  },
  {
    url: 'http://tinyurl.com/nc7jv28',
    caption: 'Scooters'
  }
];

React.render(<PhotoGallery photos={data} />, document.getElementById('app'));
```

<a target="_blank" href="https://jsbin.com/xujiro/3/edit?js,output">View JSBin</a>

The `Photo` component is exactly the same as before.

There's a new `PhotoGallery` component which generates 3 `Photo` components from some fake data passed in as a `prop`.

---

## Conclusion

This should be enough to get started building user interfaces with React. The [React docs](http://facebook.github.io/react/docs/getting-started.html) cover everything in detail. I highly recommend reading it.

There are some great videos worth watching too. Pete Hunt talks about [re-thinking web application architecture](https://www.youtube.com/watch?v=x7cQ3mrcKaY) with React and Tom Occhino introduces [React Native](https://www.youtube.com/watch?v=KVZ-P-ZI6W4) for building native mobile applications with React (WIP).

If ES6 is new to you take a look at the guide on the [Babel website](https://babeljs.io/docs/learn-es2015/) and be sure to try out the REPL.

This guide doesn't go into detail about your local environment setup. The official documentation should help, or alternatively, look at my [boilerplate](https://github.com/jarsbe/react-webpack-boilerplate) for a simple solution.

If I've made a mistake or something's not working for you, ping me on [twitter](http://twitter.com/jarsbe) or better yet make a [pull request](https://github.com/jarsbe/jarsbe.github.io/blob/master/_posts/2015-08-30-the-react-quick-start-guide-es6-edition.markdown). Feel free to email me with questions or find me on the Reactiflux Slack channel (@jarsbe) - I'm happy to help.

P.S - Checkout [The Flux Quick Start Guide](http://www.jackcallister.com/2015/02/26/the-flux-quick-start-guide.html) once you're ready to build larger applications with React.
