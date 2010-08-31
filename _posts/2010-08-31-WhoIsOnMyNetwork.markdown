---
layout: post
title: Who's on my network? Insights gained from a bad idea.
excerpt: I predict the future of web application frameworks  from an exercise in bricolage.

---
## Discovering Hosts on my local network  ##

----

From time to time my Wife calls up to my programing lair  with something like "My computer won't ..."
So, I ssh into her laptop, fix the problem or shout the answer back. 
Trouble is, I don't know the IP address of her computer, since it uses DHCP for address configuration. 
My usual solution to this is this Bash script:



### The Old Way

{% highlight bash %}
#!/bin/bash
cat /home/george/bin/localIPAddresses.txt | fping 2>/dev/null | grep "is alive"
{% endhighlight %}
<pre>
192.168.0.1 is alive
192.168.0.2 is alive
192.168.0.4 is alive
192.168.0.16 is alive
192.168.0.17 is alive
192.168.0.20 is alive
192.168.0.99 is alive
192.168.0.111 is alive
</pre>

The heart of this script is  __fping__. __fping__ blasts out pings in parallel to the network and then collects 
the replies as they come in. So, it takes only a few seconds to check the 253 addresses on my local network.

I still don't know which address is her laptop though. So I just guess and try to log into the likely candidates.
Since my Wifes' laptop stays at home, a better solution would be to just configure it to use a static IP. But, that's 
just a little too easy.

----
### Another Idea
I got the idea that I could create a standard user on each host on my network that I might want 
to ssh into and then write a script to try to log into each. Once logged in, I could get the __hostname__
and then I'd know the hosts by name.
I figured that I could speed the discovery process up by only trying to login to those addresses that 
respond to a ping.



{% highlight ruby %}
#!/usr/bin/env ruby
require 'rubygems'
require 'net/ssh'
require 'ping'

USER = 'monitor'
PASS = 'rotinom'
alive = [] 
def check(host)
      begin
        Net::SSH.start(host, USER, :password=>PASS) do |session|
            host_cmd = 'hostname'
            ip_cmd ='ifconfig'
            host_result = session.exec!(host_cmd)
            ip_result_raw = session.exec!(ip_cmd)
            ip_result = ip_result_raw[/inet addr:192\.168\.0\.[1-9]*/]
            puts "#{ip_result[/192\.168\.0\.[1-9]*/]} #{host_result}"
        end
      rescue
        return
      end
end

(1..254).each do |ip|
    host = "192.168.0.#{ip}"
      if  Ping.pingecho(host)
        alive<< host
      end
    end

alive.each do |host|
  check(host)
end
{% endhighlight %}
Here's the output:
  
{% highlight bash %}
$ time ./monitor-sequential.rb 
192.168.0.4 george-laptop
192.168.0.16 Dell
192.168.0.17 hp-pavilion
192.168.0.111 geolap

real  3m53.408s
user  0m0.280s
sys 0m0.060s

{% endhighlight %}
This works, but takes 3 minutes 53 seconds to run. 
So, why not let each ping have it's own thread?

----
### Threaded Pings 
{% highlight ruby %}

#!/usr/bin/env ruby
require 'rubygems'
require 'net/ssh'
require 'ping'

USER = 'monitor'
PASS = 'rotinom'
alive = [] 

def check(host)
      begin
        Net::SSH.start(host, USER, :password=>PASS) do |session|
            host_cmd = 'hostname'
            ip_cmd ='ifconfig'
            host_result = session.exec!(host_cmd)
            ip_result_raw = session.exec!(ip_cmd)
            ip_result = ip_result_raw[/inet addr:192\.168\.0\.[1-9]*/]
            puts "#{ip_result[/192\.168\.0\.[1-9]*/]} #{host_result}"
        end
      rescue
        return
      end
end

(1..254).each do |ip|
    host = "192.168.0.#{ip}"
    Thread.new do
      Thread.current["name"]="Working..."
      if  Ping.pingecho(host)
        alive<< host
      end
      Thread.kill(Thread.current)
    end
end

#wait for all the "Working..." threads to finish
still_working=true
while still_working do
  still_working=false
  Thread.list.each { |x| if x[:name]=="Working...";still_working=true;end} 
end

alive.each do |host|
    check(host)
end
{% endhighlight %}

Thats better:

{% highlight bash %}
 $ time ./monitor-threads.rb 
 192.168.0.17 hp-pavilion
 192.168.0.16 Dell
 192.168.0.111 geolap

 real 0m12.584s
 user 0m4.132s
 sys  0m0.084s
{% endhighlight %}
That's about a 20 times speed up. Fast enough.

----
### The future of web application frameworks

I've heard about the wonders of Ruby 1.9xs Fibers. 
Ruby [Fibers](http://ruby-doc.org/core-1.9/classes/Fiber.html) seem to be  what used to be called co-routines.
Would using Fibers speed this up? Since Fibers are co-routines, to find out, I'd have to rewrite the 
ping code so it did not block waiting for ping replies.
The first half would send the pings and yield, the second half would receive the pings.

I started out to see what could be done by putting together existing bits and pieces of code. 
I was able to speed up some code that was slow because it spent most of it's time waiting. But to do it I had to 
create a thread for each operation that spends time waiting. 

This way of doing things--putting things together from existing bits and pieces--is the normal way of 
software development. 
Even Green Field applications use existing programing languages, frameworks and operating systems. 
Even the designers and programmers are stuck in existing patterns of thought. 

Making an existing slow sycronous  piece of software
into a fast asyncronous one requires rethinking basic habits and tools. 
Erlang,Scala,EventMachine,CSP,Occam,..., are all attempts to move programing  practice past our
[sychronous addiction](http://www.eflorenzano.com/blog/post/how-do-we-kick-our-synchronous-addiction/)

It seems that I am not the only one who has a hard time thinking in asyncronous terms. Even the popular [GTD](http://www.davidco.com/) 
system of time management advises against multitasking. Arguing that humans are not effective multitaskers.

If thin responsive web apps are to displace 
[fat](http://www.mikeperham.com/2010/04/03/introducing-phat-an-asynchronous-rails-app/) desktop ones,
some serious web development mindset and framework rehab may be required. 

The trouble with being addicted to something is in seeing how to live with out it.

Hello, my name is George, and I'am a Sycroholic.



![sycroholic](/images/sycroholic.png)



