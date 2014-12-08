---
layout: post
title: CSS Animation for the Rails developer
---

Animations are made in CSS with the `@keyframes` directive. You’ve seen directives before; `@media`, `@print`.  The `@keyframes` directive accepts a name and a block.

{% highlight css %}
@keyframes heartbeat {
}
{% endhighlight %}

The block accepts any number of percentages which represent the stages of the animation across time. 

{% highlight css %}
@keyframes heartbeat {
  0%{
  }
  50%{
  }
  100%{
  }
}
{% endhighlight %}

The percentage blocks accept properties and values to change on the element being animated.

{% highlight css %}
@keyframes heartbeat {
  0%{
    transform: scale(1.2);
  }
  50%{
    transform: scale(0.75);
  }
  100%{
    transform: scale(1.0);
  }
}
{% endhighlight %}

With the `@keyframes` defined one can use the animation.

{% highlight css %}
.heart {
  animation-name: heartbeat;
  animation-duration: 2s;
}
{% endhighlight %}

The `.heart` element is given two animation properties. One to state what `@keyframe` to use and another to state the length of the animation.

Short hand can be used too.

{% highlight css %}
.heart {
  animation: heartbeat 2s;
}
{% endhighlight %}

There are a number of other useful animation properties that can be used. I highly suggest using this excellent article from [Thoughtbots](http://robots.thoughtbot.com/css-animation-for-beginners).