---
layout: post
title: Finding the Mavens in a social network
published: false
excerpt: At a Spokane Ruby Brigade meeting, a member wanted to rank sales people in a MLM (Pyramid)  scheme 

---
## Finding the Mavens in a social network  ##

----



some kind of tests:
'smarty pants'
elipsis ...
em-dash --



All social networks have [Mavins](http://en.wikipedia.org/wiki/Maven), those people that are much more infuential
than the average. The key to success in  multilevel marketing  is getting others to do the sales for you. 
In multilevel marketing schemes, sales commissions are assigned according to your position (seat) in a 
pyramid. 

My solution only goes as far as counting direct connections between parent and child  sales people.

some kind of tests:

'smarty pants'

elipsis ...

em-dash --





### The problem ###


{% highlight ruby %}
#!/usr/bin/env ruby
require 'rubygems'
#for a disscussion of the dynamics of multi level marketing see:
#https://docs.google.com/viewer?url=http://www.systemdynamics.org/conferences/2008/proceed/papers/OLAYA411.pdf

#from http://my.rails-royce.org/2009/10/02/ordered-hash-by-keys/
require 'active_support/ordered_hash'
class SimpleOrderedHash < ActiveSupport::OrderedHash
   # Lets reimplement sort method
  def sort(&block)
    h = self.dup
    h.sort!(&block)
    h
  end
 
  # Lets implement sort! method
  # It uses @keys variable to do the sorting
  def sort!(&block)
    @keys.sort!(&block)
    sync_keys!
    self
  end
end

#create a random network of people who have talked others into selling for them
#each of 10,000 seats gets a random boss (parent)
#I expect a real marketing network  to be less uniform than this. 
#
seat = Hash.new
10000.times {|i| seat[i]=rand(9999) }
 
#count the number of times each seat is the boss of another one
weighted_seat = SimpleOrderedHash.new
seat.each {|k,v| weighted_seat[v]||=0;weighted_seat[v] +=1}

# order the seats by the number of childern each has
result = weighted_seat.sort do |a,b|
  weighted_seat[b] <=> weighted_seat[a]
end

#show only the top 10, who cares about the loosers.. 
top_ten = result.first(10)
puts "The Winners are:"
top_ten.each { |k,v| puts "#{k}:#{v}" }
 
{% endhighlight %}


{% highlight bash %}
 $ time ./mlm.rb
 The Winners are:
 670:7
 5058:6
 2664:6
 729:6
 2436:6
 9633:6
 9010:6
 6792:6
 2641:6
 6743:5

 real 0m0.136s
 user 0m0.120s
 sys  0m0.012s

{% endhighlight %}

