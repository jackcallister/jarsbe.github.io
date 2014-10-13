---
layout: post
title: Better object initialization
---

I'm very much used to doing this when building a new Ruby class.

{% highlight ruby %}
class Honey
  attr_accessor :sweetness, :name, :colour

  def initialize(sweetness, name, colour)
    @sweetness = sweetness
    @name = name
    @colour = colour
  end
end
{% endhighlight %}

This looks fine but here is a better version.

{% highlight ruby %}

class Honey
  attr_accessor :sweetness, :name, :colour

  def initialize(options = {})
    @sweetness = options.fetch(:sweetness)
    @name = options.fetch(:name)
    @colour = options.fetch(:colour)
  end
end

{% endhighlight %}

Much better. Now we don't have to initialize Honey objects with a specific order of arguments. Just pass them in as a hash argument and the initialize method does the rest.
