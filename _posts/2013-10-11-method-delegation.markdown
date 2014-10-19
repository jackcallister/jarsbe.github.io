---
layout: post
title: Method delegation
---

<p>Here's a sweet trick to avoid lengthy chaining in your method calls. The delegate method. Here's a simple apiary themed model setup.</p>

{% highlight ruby %}
class Queen < ActiveRecord::Base
  belongs_to :hive
end

class Hive < ActiveRecord::Base
  belongs_to :site
end
{% endhighlight %}

<p>Sometimes we need to ask the Queen for it's Site ID, especially when building forms. We can do this easily with the delegate method.</p>

{% highlight ruby %}
class Queen < ActiveRecord::Base
  belongs_to :hive

  delegate :site_id, to: :hive, allow_nil: true
end
{% endhighlight %}

