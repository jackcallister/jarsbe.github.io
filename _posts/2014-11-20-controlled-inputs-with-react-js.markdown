---
layout: post
title: Controlled inputs with React JS
---

I've been working with react.js alot lately. It's a library for developing user interfaces and I've found that the concepts behind it make developing interfaces quite simple. There are a few 'gotchas' though and one in particular had me stumped, controlled inputs. Imagine we have an input which react is rendering.

{% highlight js %}

render: function() {
  return ( <input type="text" value="Hello" /> )
}

{% endhighlight %}

You would expect that if I were to type into the input the value would change. This is not the case as react only allows data to flow in one direction. The *view* cannot update the *state* of the component directly. How do we change the input value?

{% highlight js %}

getInitialState: function() {
  return {
    value: "Hello"
  }
},

handleChange: function(event) {
  this.setState({
    value: event.target.value
  });
},

render: function() {
  return ( <input type="text" value={this.state.value} onChange={this.handleChange} /> )
}

{% endhighlight %}

The component is given an initial state called `value` which holds onto the string `Hello`. When that state changes the value in the field will update. 

We're halfway towards a usable input field! 

Next a function called `handleChange` is attached to the input's `onChange` event. The `handleChange` function updates the components state with the new value of input. This causes the component to re-render with the new value and viola, a text field which updates when I type in it.

This might all seem a bit strange and abitary but it makes for really, really simple UI programming. For more information on this kind of one way data architechture you simply must watch this [video](https://www.youtube.com/watch?v=x7cQ3mrcKaY).


