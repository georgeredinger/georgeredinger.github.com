---
layout: post
title: Pickle Jars 
---
## Pickle Jar ##

Given a list of names, pick one at random

First Try:
"Object Oriented"
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
A "One Liner"
{% highlight ruby %}
  ruby -e 'a=%W(bob joe chr jan frk crl cbw dog jr );\
    puts "The Winner is: #{a[rand(a.size)]}"'
{% endhighlight %}

Third Try:
From the previous century: CGI
<script src="http://gist.github.com/525211.js"> </script>

Forth Try:
Rack, like all the cool kids do things  
<script src="http://gist.github.com/525241.js?file=pickle_jar_rack.rb"></script>

Fifth Try:
Javascript (jQuery), the new hotness
<script src="http://gist.github.com/525227.js"> </script>
