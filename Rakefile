require 'rubygems'
require 'date'

def ask message
  print message
  STDIN.gets.chomp
end

desc "Notify Google of the new sitemap"
task :sitemap do
    require "net/http"
    require "uri"
    Net::HTTP.get(
      "www.google.com",
      "/webmasters/tools/ping?sitemap=" +
      URI.escape("http://georgeredinger.github.com/sitemap.xml")
    )
end

def jekyll(opts = '--pygments')
    sh 'rm -rf _site'
      sh 'jekyll ' + opts
end

desc 'Build site with Jekyll'
task :build do
    jekyll
end

desc 'Start server with --auto'
task :server do
    jekyll('--server --auto')
end

desc 'deploy assumed already build'
task :deploy  do
    #sh 'rsync -rtzh --progress --delete _site/ tatey@tatey.com:~/var/www/tatey.com/'
    sh 'git push origin'
end

desc "Serve on Localhost with port 4000"
task :default do
    jekyll("--server --auto")
end

desc 'create a new draft post'
task :new do
    title = ENV['TITLE']
    if title.nil?
      puts 'you forgot the TITLE'
      puts 'rake new TITLE="title of the post"'
      exit
    end
    slug = "#{Date.today}-#{title.downcase.gsub(/[^\w]+/, '-')}"

    file = File.join(
    File.dirname(__FILE__),
    '_posts',
    slug + '.markdown'
    )

    File.open(file, "w") do |f|
      f << <<-EOS.gsub(/^    /, '')
---
layout: post
title: #{title}
published: false
excerpt: a short sumary of the post
categories: keywords are for cats 
---

      EOS
   end

   system ("#{ENV['EDITOR']} #{file}")
end

