---
layout: default
title: Rails Girls Pushing to Heroku on Cloud 9
permalink: sinatra-rspec-guide
---

# Rails Girls - Pushing to Heroku on Cloud 9
*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

#### Getting started
Deploying to Heroku on Cloud 9 is easy! All you need is a Heroku Account. If you don't already have you sign up here -> [sign up page](https://signup.heroku.com/devcenter?_ga=2.7516091.860715976.1499425129-753841004.1499338188)
Then all you need is to remember your username and password :)

#### Updating our database

Return to Cloud9 and open the Gemfile
We need to make a small change in order to get our database to work on Heroku. It uses a database called PostgreSQL which is more suited 
 to a production environment than SQLite which is the database we have been using so far. Please change the following in the Gemfile:

{% highlight ruby %}
gem 'sqlite3'
{% endhighlight %}

to

{% highlight ruby %}
group :development do
  gem 'sqlite3'
end
group :production do
  gem 'pg'
end
{% endhighlight %}

Here we are telling our app that when in production environment use PostgreSQL (that's the pg bit) and when in a development environment
use the SQLite database. Clever huh!

Finally save the Gemfile and run `bundle install --without production` to setup your dependencies.

### Deploying your app

#### App creation

You first need to login to Heroku, type `heroku login`

Next we need to create our Heroku app by typing `heroku create` in the cloud 9 terminal and see something like this:

{% highlight sh %}
sorcha:~/workspace (master) $ heroku create
Creating app... done, ⬢ murmuring-bayou-70721Git remote heroku added
{% endhighlight %}

In this case "murmuring-bayou-70721" is your app name.

#### Pushing the code
Next we need to push our code to heroku by typing `git push heroku master`. You'll see push output like the following:

{% highlight sh %}
Initializing repository, done.
Counting objects: 101, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (91/91), done.
Writing objects: 100% (101/101), 22.68 KiB | 0 bytes/s, done.
Total 101 (delta 6), reused 0 (delta 0)

-----> Ruby app detected
-----> Compiling Ruby/Rails
-----> Using Ruby version: ruby-2.0.0
-----> Installing dependencies using 1.6.3
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching gem metadata from https://rubygems.org/..........
...
-----> Launching... done, v6
       http://sheltered-refuge-6377.herokuapp.com/ deployed to Heroku
{% endhighlight %}

You'll know the app is done being pushed, when you see the "Launching..." text like above.

#### Migrate database

Next we need to migrate our database like we did locally during the workshop: `heroku run rake db:migrate`.

When that command is finished being run, you can hit the app based on the url. For this example app, you can go to <http://sheltered-refuge-6377.herokuapp.com/>. You can also type `heroku open` in the terminal to visit the page.


# Keep in touch

Follow us on twitter

Sorcha <a href="https://twitter.com/sabel25" class="twitter-follow-button" data-show-count="false">Follow @sabel25</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rachelle <a href="https://twitter.com/RachelleOnRails" class="twitter-follow-button" data-show-count="false">Follow @RachelleOnRails</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rails Girls Brisbane <a href="https://twitter.com/railsgirlsbne" class="twitter-follow-button" data-show-count="false">Follow @railsgirlsbne</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Read the Rails Girls Brisbane blog [http://railsgirlsbne.com/](http://railsgirlsbne.com/)

Email us: <a href="mailto:railsgirlsbne@gmail.com?Subject=Hello%20RailsGirls" target="_top">Rails Girls Brisbane</a>

Join our Facebook Group: <a href="https://www.facebook.com/groups/462831463794656/">Rails Girls BNE Group</a>

