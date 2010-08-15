---
layout: post
title: Pickle Jars 
---
## Pickle Jar ##

Given a list of names, pick one at random
Given a list of names, shuffle them into random order

First Try:

{% highlight ruby %}
class pickle_jar
  initialize(list)
   puts list.pick_a_random_name
  end
  def pick_a_random_name
    "George"
  end
end
{% endhighlight %}


Second Try:

{% highlight ruby %}
  ruby -e 'a=%W(bob joe chr jan frk crl cbw dog jr );\
    puts "The Winner is: #{a[rand(a.size)]}"'
{% endhighlight %}



