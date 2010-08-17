---
layout: post
title: Co-op Propane 
excerpt: Using Sinatra, Nokogiri and the Flot javascript charting library to   ...
---

## Visualize  Propane price history ##

Consumer propane prices are highly variable. Like most consumers I prefer to buy when prices are low. 
But how to know when prices are at their lowest?   This application looks at price history from my local
propane supplier ["co-op energy"](http://www.co-openergy.org/prices.html)  to help.

I used
* Nokogiri
* Sinatra
* Datamapper
* Flot
* jQuery
* Haml/Sass
* And Heroku

to build it.

That's a big pile of software to build a "simple" Sinatra app.
I wanted to compare the "ease" of building small apps with Sinatra vs Rails.
My conculsion: In this case Sinatra is easier on the computer. But, once you get Haml/Sass, Datamapper, Migrations,
Views and models in their own directories, etc, It's not much easier for me.
An instance of the  app is deployed here:

[http://cooppropane.heroku.com/] (http://cooppropane-rack.heroku.com/)

The source is on [GitHub] (http://github.com/georgeredinger/cooppropane)


