---
layout: default
title: Rails Girls Next Testing Sinatra
permalink: sinatra-rspec-guide
---

# Rails Girls Next - Testing with Rspec

*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## What is Rspec?
The first thing to know about Rspec is that it gem.

# Setup

## *1.* Update the Gemfile with the following gems

Open the sinatra idea app in cloud9 and navigate to your Gemfile.

{% highlight ruby %}
group :test do
  gem "rspec"
  gem "rack-test"
end
{% endhighlight %}

Save the `Gemfile` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

Commit the updated `Gemfile` to git:

{% highlight sh %}
git add Gemfile
git commit -m "updated Gemfile"
git push origin master
{% endhighlight %}

Any time you edit a `Gemfile` you must install the new gems. To do so, run the following command in the terminal window:

{% highlight sh %}
bundle install
{% endhighlight %}

### Quick Overview of the gems

**rspec** is a testing frameworks and supports a powerful DSL (Domain Specific Language) that allows us to unit test our apps.

**rack-test** includes helper methods that allow us to simulate requests against our app and assert expectations about the response.


## *1b.* Installing rspec

Run the following command in the terminal window:

{% highlight sh %}
bundle exec rspec --init
{% endhighlight %}

Running the above command will create an `spec` folder with 2 new files.
1. `.rspec`
2. `spec_helper.rb`

## *1c.* Adding validation to our model

Before we commence with writing of our new unit tests lets first create some validation in for our Idea model

Navigate to the app/models/Idea.rb file and insert the following lines so that your Idea.rb file resembles below

{% highlight sh %}
class Idea < ActiveRecord::Base
  validates_presence_of :name
end
{% endhighlight %}

We are now ready to start testing our app!

# Setup



