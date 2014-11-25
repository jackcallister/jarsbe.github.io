---
layout: post
title: Autofocus in React
---

I had a little dynamic form in a React component I was building. The form had a meal title with an ingredient field below it. One could click add ingredient and another ingredient field would be added to the form. I wanted to make sure that the newly added field would gain focus but was a little lost. 

In the state was an array of ingredients. When I clicked add ingredient I'd add a null ingredient which would cause a rerender and show the new field. I started with something like this:

{% highlight js %}

addIngredientField: function(e) {
  e.preventDefault();
  var ingredients = this.state.ingredients;
  ingredients.push(this.defaultIngredient());

  this.setState({
    ingredients: ingredients
  }, this.focusIngredientField(this.state.ingredients.length - 1));
},

focusIngredientField: function(index) {
  // THIS DOESN'T WORK AS THE NODES HAVEN'T RENDERED?
  // this.refs["ingredient-name-" + index].getDOMNode.focus();
}

{% endhighlight %}

In the end it was easily done by setting a `Autofocus` property to true on the last rendered field like so, conveniently eliminating the `focusIngredientField` function all together. In the render method (with some variable initialization omitted).

{% highlight js %}

if (isLastField) {
   focus = true;
}

<input ... autoFocus={focus} />

{% endhighlight %}
