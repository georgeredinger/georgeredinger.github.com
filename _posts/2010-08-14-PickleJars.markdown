---
layout: post
title: Pickle Jars 
---
## Pickle Jar ##

Given a list of names, pick one name at random.

This problem was suggested by [CowboyOnRails](http://www.cowboyonrails.com/) for use at
[Spokane Ruby Brigade](http://spokane.rubyusersgroup.org/pickle_jar). For choosing who gets 
swag resulting from the Brigade's O'reilly user group sponsorship.

----
## Zeroth Try: 

"The Cowboy's solution"

{% highlight ruby %}
module PickleJar

  @random = []
  
  def self.add_name(name)
    @random << namecase(name)
    @random.uniq!
    @random
  end
  
  def self.add_names(names)
    if names.kind_of? String
      names = names.split(',')
    elsif !names.kind_of? Array
      raise RuntimeError, "Please use comma delimited string or array with this method."
    end
    @random += names.collect{ |n| namecase(n.strip) }
    @random.uniq!
    @random
  end
  
  def self.remove_name(name)
    @random.delete(namecase(name.to_s))
  end
  
  def self.remove_names(names)
    if names.kind_of? String
      names = names.split(',')
    elsif !names.kind_of? Array
      raise RuntimeError, "Please use comma delimited string or array with this method."
    end
    @random -= names.collect { |n| namecase(n.strip) }
    @random
  end
  
  def self.winner_is
    return "The Pickle Jar is empty..." if @random.length < 1
    mix_it_up
    "THE WINNER IS: (drum roll)...   #{@random.delete_at(0)}"
  end
  
  def self.mix_it_up  # Just for fun...  already random but makes people feel better.
    @random = @random.sort_by{rand}[0..@random.length]
  end

  def self.peek_in_jar
    @random
  end
  
  def self.namecase(name)
    name = name.to_s
    name.strip!
    name.downcase!
    name.gsub(/\b\w/){$&.upcase}
  end

  def self.empty
    @random = []
  end

end
{% endhighlight %}

This solution also has tests and a user manual. The rest of Cowboy's solution is can be found [here](http://github.com/cowboyonrails/PickleJar)


----
## My First Try:
A "One Liner"

This solution more closely matches the conventional meaning of "Cowboy code".

{% highlight ruby %}
  ruby -e 'a=%W(bob joe chr jan frk crl cbw dog jr );\
    puts "The Winner is: #{a[rand(a.size)]}"'
{% endhighlight %}


----
## My Second Try:
"Object Oriented"

This one uses some of the techniques used in other automated voting systems.

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


----
## My Third Try:
From the previous century: CGI


<script src="http://gist.github.com/525211.js"> </script>


## Forth Try:
Rack, like all the cool kids do things  

<script src="http://gist.github.com/525241.js?file=pickle_jar_rack.rb"></script>


----
## My Fifth Try:
Javascript (jQuery), the new hotness

<script src="http://gist.github.com/525227.js"> </script>


----
Some of these solutions are deployed on the web:

* [http://picklejar.georgeredinger.com/] (http://picklejar.georgeredinger.com/)
* [http://thewinneris.georgeredinger.com/choose.rb] (http://thewinneris.georgeredinger.com/choose.rb)
* [http://picklejar-rack.heroku.com/] (http://picklejar-rack.heroku.com/)


