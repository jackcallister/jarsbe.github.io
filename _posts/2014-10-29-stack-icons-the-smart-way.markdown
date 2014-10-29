---
layout: post
title: Stack icons the smart way
---

After figuring out how to use icon fonts I then stumbled across "[stackicons](http://stackicons.com/)".

Icon fonts are great but limited to being one colour. Not with a stackicon however. The idea is to stack icon characters on top of one another and set the right colour for each.
I created a set of stackicons to represent the colour and status of a hive's queen bee. Each queen has a standard bee shaped background and a coloured circle with an inner icon representing the queen status (healthy, failing, dead).

- Created the icon font with five SVGs (bee, circle, cross, tick, re-queen).
- Set the positioning of the icons within an i element.
- Set the colours for the individual characters.
- Sit back and relaxed.

{% highlight sass %}

[class^="stack"], [class*=" stack-"] {
  font-family: $icons-font-family;
  speak: none;
  font-style: normal;
  font-weight: normal;
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.stack {
  position: relative;
}

.stack-queen {

  &:before {
    content: "\e60d";
    color: $light-grey;
  }

  span:before {
    content: "\e602";
    color: $light-grey;
    position: absolute;
    left: 0.82em;
    font-size: 0.5em;
    top: 0.45em;
  }

  &:after {
    color: $white;
    position: absolute;
  }
}

.stack-queen-healthy {
  content: "\e601";
  font-size: 0.3em;
  left: 1.6125em;
  top: 1.185em;
}

.stack-queen-failing:after {
  content: "\e600";
  font-size: 0.2em;
  left: 2.5125em;
  top: 2.125em;
}

.stack-queen-dead:after  {
  content: "\e60c";
  font-size: 0.3em;
  left: 1.68125em;
  top: 1.1125em;
}

.stack-queen-blue span:before {
  color: $blue;
}

.stack-queen-green span:before {
  color: $green;
}

.stack-queen-yellow span:before {
  color: $yellow;
}

.stack-queen-white span:before {
  color: $white;
}

.stack-queen-red span:before {
  color: $red;
}

{% endhighlight %}

{% highlight html %}

<i class="stack-queen stack-queen-red stack-queen-healthy"></i>

{% endhighlight %}

Above I've created a queen icon with a red circle and tick. I love the resilience to change of this system. For example, when these icons were tested it was found that a queen could be white but the inner icon was white and not visible. Rather than talking to a designer, getting an icon and adjusting the sprite sheet I simply added this condition to the SASS.

{% highlight sass %}

.stack-queen-white {
  &.stack-queen-healthy,
  &.stack-queen-failing,
  &.stack-queen-dead {
    &:after {
      color: $grey;
    }
  }
}

{% endhighlight %}

I like this ease of change when I build systems. It is worth noting that it increases the level of abstraction and it is harder for other developers to understand what is going on. That's easily rectified with a top-notch and well maintained styleguide. In the end the ability to make changes quickly wins out in the long run.
