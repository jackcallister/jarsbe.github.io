---
layout: post
title: YML for everything in Rails
---

<p>I've been loving feature specs lately. They are perfect for getting a bit of everything covered without much effort. However, I found whenever I changed text on a page, such as buttons or titles I would often have to change specs. Something was smelly. Enter translation files.</p>

<p>The solution is to move ALL text to YML. This cleans up specs, makes it easier to keep text consistent (camel cased or capitalized etc) and of course makes it simple to internationalize.</p>

<p>Here's a quick example of this idea in action.</p>

{% highlight ruby %}
en:
  helpers:
    navigation:
      application: Honey Pot
      hive: Hives
      queen: Queens
    title:
      hive:
        index: Hives
        edit: Edit Hive
      queen:
        index: Queens
        edit: Edit Queen
    submit:
      hive:
        create: Create Hive
        update: Update Hive
      queen:
        create: Create Queen
        update: Update Queen
    label:
      hive:
        category: Hive Type
        origin: Origin
        total_frames: Total Frames
      queen:
        hive: Hive
        status: Status
        breed: Breed
        source: Source
        colour: Colour
{% endhighlight %}

<p>Now everything is available to setup page titles, submit buttons and form labels. Another neat tip is that labels are automatic used from this yml file when used within a form block.</p>
