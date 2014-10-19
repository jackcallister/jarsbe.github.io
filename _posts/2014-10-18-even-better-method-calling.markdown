---
layout: post
title: Even better method calling
---

<p>With Ruby 2.0 there's now an even better way to initialize objects (or call methods) which uses keyword args. We can now do this:</p>

{% highlight ruby %}
class Apiary < ActiveRecord::Base

belongs_to :site

def honey_production(range:, manuka: false)
  hives.each do |hive|
    hive.honey_for_range(range, manuka)
  end
end

# Call the method like so
site.honey_production(range: two_week_range, manuka: true)
{% endhighlight %}

<p>Rather than having to extract the method args from an options hash, just use the args directly. Nice and tidy. Reordering args in the call is fine since the method will sort the args as needed.</p>
