---
layout: default
title: Rails Girls Next Testing Sinatra
permalink: sinatra-rspec-guide
---

# Rails Girls Next - Testing with Rspec

*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## What is Rspec?
Rspec is both a gem and a framework that provides a powerful DSL that allows us to unit test our app. In this guide we will
be creating unit tests for our Idea model and controller. The model testing will focus on validation and the controller testing
will focus on the simulating the app requests and assert expectations about the responses.

# Setup

## *1.* Update the Gemfile with the following gems

Open the sinatra idea app in cloud9 and navigate to your Gemfile. Insert the following into the Gemfile

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

# Setting up our spec_helper file

Navigate to the newly created `spec_helper.rb` file inside the spec folder and type the following at the start of the file. Don't worry if you are unsure about some of it, it will be explained shortly.
{% highlight sh %}

require_relative '../config/application'

#rspec
require 'rack/test'
require 'rspec'

ENV['RACK_ENV'] = 'test'

module RSpecMixin
  include Rack::Test::Methods
  def app() Sinatra::Application end
end

RSpec.configure { |c| c.include RSpecMixin }
RSpec.configure { |c| c.profile_examples = true }
{% endhighlight %}

So lets take a moment to understand what we have just typed

The first line; `require_relative '../config/application’` # this is including your whole sinatra app into the test suite!! Go take another look at the config/application.rb file and see how it requires the whole project

The next 2 lines require the gems we added to our Gemfile. We have already covered their purpose.

The line following sets the RACK_ENV environment variable, which Sinatra checks when setting its environment. This is a very important step
as it ensures the tests all run against your test database (and not development one). Take a moment to absorb this, imagine what
would happen if every time we ran our tests they ran against your dev database. For example in our tests we delete all records
from our test database. Imagine if this happened to all your development data. To avoid that headache ensure you never forget to set
the RACK_ENV variable

`ENV['RACK_ENV'] = 'test'`

The next step is to create a module that will include the Rack::Test methods that Rspec needs. The Rack::Test::Methods module includes a variety of
helper methods for simulating requests against an application (we define the application inside the contained app method) and asserting expectations about the response.

Finally we are configuring RSpec to include the module ensuring it has access to all the helper methods.
The `profile_examples` will cause RSpec to inform you of your slowest tests. Its good practise to use this line, slow tests can be an indication of slow production performance.

# Spec Folder Structure

We have just added validation to our model and specified that a valid record must have a value assigned to the name attribute. Lets test that now

Create a new file inside the spec folder called sinatra_helper.rb. Open it and type the following
{% highlight sh %}
require 'spec_helper'
{% endhighlight %}

We are now ready to create some tests!


From the terminal window, ensure you are in the spec folder and create a new folder called `app` move up one level of the project structure and into the root of the project. From here create another folder called `db`. This will store information about our sqlite3 database including our database migrations.
{% highlight sh %}
cd ..
mkdir app
{% endhighlight %}

Using the terminal navigate into the new folder and create two new directories, one for controllers and one for models
{% highlight sh %}
cd app
mkdir controllers models
{% endhighlight %}

By the time our testing setup is complete, it should look similar to the structure below:

<img src="/images/rspec_tree.png">

## Committing to git
{% highlight sh %}
git add .
git commit -m "Setup and configuration"
git push origin master
{% endhighlight %}


# Testing the Idea Model
Using the terminal navigate into the new model folder and create a new file called `idea_spec.rb`
{% highlight sh %}
cd models
touch idea_spec.rb
{% endhighlight %}

Open the newly created idea_spec.rb file and enter the following

{% highlight sh %}
require 'sinatra_helper'

RSpec.describe Idea do

  describe '#initialize' do

    it 'without name' do
      expect(described_class.new(name: nil, description: 'description')).to_not be_valid
    end

    it 'with valid' do
      expect(described_class.new(name: 'name', description: 'description')).to be_valid
    end

  end
end
{% endhighlight %}

A few points to note. We require the `sinatra_helper` file. This is the file with all the setup we created above.

Also in the first `RSpec.describe` block we are referencing the Idea class name. When we use a Class name in the describe block, we can make use of the built in `described_class` method. In this case `described_class` refers to the Idea model class.

We have created 2 explicit tests, one with a name and one without. As we earlier added validation to our model we can expect the test with a name to be valid and a test without a name to not be valid.

Take some time to get familiar with the RSpec tests. You can see we are explicitly setting values. It is considered by many a best practise to be as explicit as possible in your tests. In fact its the one place in ruby where the DRY principle doesn't apply.


## Run the test
NB always run your tests from the root of your project and not from within the spec folder
{% highlight sh %}
cd ~/workspace/
bundle exec rspec spec/app/models/idea_spec.rb

{% endhighlight %}

## Committing to git
{% highlight sh %}
git add .
git commit -m "Setup and configuration"
git push origin master
{% endhighlight %}




