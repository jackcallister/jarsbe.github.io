---
layout: post
title: Monkey patching
---

<p>I haven't ever had to monkey patch Rails before but recently I was implementing localisation of select prompts. I started building a method like this in my application_helper.rb.</p>

{% highlight ruby %}

def select_prompt(options)
  resource = options.fetch(:resource)
  field = options.fetch(resource)

  class_name = resource.class.to_s.downcase

  I18n.t("helpers.global.prompt") + " " + I18n.t("helpers.label.#{class_name}.#{field}")
end

{% endhighlight %}

<p>Nothing wrong with this but as I used it more and more I realized I was passing f.object in as the resource arg every single time. I then understood I should be sending the message to the form builder object. Here's the monkey patched version. Complete correct vowel prefixing.</p>

{% highlight ruby %}

class ActionView::Helpers::FormBuilder
  def select_prompt(field)
    label = I18n.t("helpers.label.#{@object_name}.#{field}")
    (I18n.t("helpers.global.prompt") + " " + indefinitize(label)).capitalize
  end

  def indefinitize(word, consonant = "a", vowel = "an")
    result = word.to_s.dup
    result.match(/^([aeiou])/i) ? "#{vowel} #{result}" : "#{consonant} #{result}"
  end
end

{% endhighlight %}

<p>Object name gives me the correct class name and the indefinitize method sorts out prefixes.</p>

<p>I placed this file in the lib directory and required it in the initializers and everything worked as expected with a much cleaner API for my select_prompt.</p>
