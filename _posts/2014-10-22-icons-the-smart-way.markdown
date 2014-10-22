---
layout: post
title: Icons the smart way
---

I recently discovered how to make icon fonts to replace sprite sheets. This is hands-down the best method for implementing icons in a web application.

If you've ever used the bootstrap glyphicons you've already used this method. Here's the SASS required to get started.

{% highlight sass %}

@font-face {
  font-family: "icons";
  src: font-url("icons.eot?-80d90m");
  src: font-url("icons.eot?#iefix-80d90m") format("embedded-opentype"),
       font-url("icons.woff?-80d90m") format("woff"),
       font-url("icons.ttf?-80d90m") format("truetype"),
       font-url("icons.svg?-80d90m#icons") format("svg");
  font-weight: normal;
  font-style: normal;
}

[class^="icon-"], [class*=" icon-"] {
  font-family: "icons";
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-edit:before {
  content: "\e609";
}

.icon-trash:before {
  content: "\e60b";
}

.icon-contacts:before {
  content: "\e603";
}

.icon-map:before {
  content: "\e604";
}

{% endhighlight %}

The icon font is imported through @font-face then used on any element which matches the icon selector.

Individual icons are assigned the correct font character through the before psuedo selector and now we can write some HTML to display an icon.

{% highlight html %}

<i class="icon-edit"></i>
<i class="icon-trash"></i>
<i class="icon-contacts"></i>
<i class="icon-map"></i>

{% endhighlight %}

Now, how is the actual font created?

Firstly, get a great designer to make you the icons, in svg format, then head to [icomoon](https://icomoon.io/app/). The rest is self-explanatory and increadibly easy I must say.

Once this system is setup changing icon sizes and colours is very simple since it's just a font! The end result is a system which is impervious to change.
