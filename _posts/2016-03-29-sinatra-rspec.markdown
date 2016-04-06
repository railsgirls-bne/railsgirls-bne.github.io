---
layout: default
title: Rails Girls Next Testing Sinatra
permalink: sinatra-rspec-guide
---

# Rails Girls Next - Testing with Rspec
*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## Unit Tests
Unit testing is the practice of testing the components of your program automatically by defining inputs to each component and checking the outputs.

Unit tests are necessary because they help prove that your code actually works. It allows you to refactor with confidence, running tests that pass
before and after refactorings helps prove your code still works as expected.

Unless you want to spend all your waking hours running tests manually, you need a test framework.

## What is Rspec?
Unless you want to spend all your waking hours running tests manually, you need a test framework.
Rspec is a gem and also a framework that provides a powerful DSL allowing us to unit test our app. In this guide we will
be creating unit tests for our Idea model and controller.

The tests we will write for our model will concentrate primarily on validation.
For our controller testing we will focus on the simulating the app requests and assert expectations about the responses.

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

Before we commence with the writing of our new unit tests lets first create some validation in for our Idea model

Navigate to the app/models/idea.rb file and insert the following lines so that your Idea.rb file resembles below

{% highlight sh %}
class Idea < ActiveRecord::Base
  validates_presence_of :name
end
{% endhighlight %}

Save the `idea.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

Commit the updated `idea.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "updated Gemfile"
git push origin master
{% endhighlight %}

We are now ready to start testing our app!

# Setting up our spec_helper file

Navigate to the newly created `spec_helper.rb` file inside the spec folder and type the following at the start of the file. Don't worry if you are unsure about some of it, it will be explained shortly.
{% highlight sh %}

require_relative '../config/application'

require 'rack/test'
require 'rspec'

ENV['RACK_ENV'] = 'test'

module RSpecMixin
  include Rack::Test::Methods
  def app()
    Sinatra::Application
  end
end

RSpec.configure { |c| c.include RSpecMixin }
RSpec.configure { |c| c.profile_examples = true }
{% endhighlight %}

Save the `spec_helper.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

Lets take a moment to understand what we have just typed

The first line; `require_relative '../config/application’` is including your whole sinatra app into the test suite!!
Take another look at the config/application.rb file we created as part of our sinatra app earlier, it requires the whole project.

The next 2 lines require the gems we added to our Gemfile. Their purpose is described above.

The line following sets the RACK_ENV environment variable, which Sinatra checks when setting its environment. This is a very important step
as it ensures the tests all run against your test database (and not development one). Take a moment to absorb this, imagine what
would happen if every time we ran our tests they ran against your development database. In general a development environment will mimic production
and therefore will contain a lot of important data.

In the tests we are about to create we delete all records from our test database as a clean up.

`ENV['RACK_ENV'] = 'test'`

The next step is to create a module that will include the Rack::Test methods that Rspec can use. The Rack::Test::Methods module includes a variety of
helper methods that all us to simulate requests against the application (we defined the app inside the module) and assert expectations about the response.

Finally we are configuring RSpec to include the module ensuring it has access to all the helper methods.

The `profile_examples` will cause RSpec to inform you of your slowest tests. Its good practise to use this, slow tests can be an indication of slow production performance.

# Spec Folder Structure

Earlier we added validation to our model and specified that a valid record must have a value assigned to the name attribute. Lets test that now

Create a new file inside the spec folder called sinatra_helper.rb. Open it and type the following
{% highlight sh %}
require 'spec_helper'
{% endhighlight %}

Save the `sinatra_helper.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

We are now ready to create some tests!

From the terminal window, ensure you are in the spec folder and create a new folder called `app`

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

Save the `idea_spec.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

A few points to note;

We required the `sinatra_helper` file. This is the file with all the setup we created above.

Also in the first `RSpec.describe` block we are referencing the Idea class name. When we use a Class name in the describe block, we can make use of the built in `described_class` method. In this case `described_class` refers to the Idea model class.

We have created 2 explicit tests, one with a name and one without. As we earlier added validation to our model we can expect the test with a name to be valid and a test without a name to not be valid.

Take some time to get familiar with the RSpec tests. You can see we are explicitly setting model values. It is considered by many best practise to be as explicit as possible in your tests . In fact it's the one place in ruby where the DRY principle doesn't apply.


## Run the test
**NB always run your tests from the root of your project and not from within the spec folder**

Using the terminal type the following
{% highlight sh %}
cd ~/workspace/
bundle exec rspec spec/app/models/idea_spec.rb

{% endhighlight %}

The output of the tests should resemble below

<img src="/images/model_passing_test.png">


## Committing to git
{% highlight sh %}
git add .
git commit -m "model tests"
git push origin master
{% endhighlight %}

# Testing the Controller

So far we have tested validation and while that is very important we still have much more to test in order to get good 'test coverage'

From your terminal window navigate to the spec/app/controllers directory and create a ideas_controller_spec.rb file

{% highlight sh %}
cd ~/workspace/spec/app/controllers/
touch ideas_controller_spec.rb
{% endhighlight %}

Open the newly created file so we can start writing our tests. One of the first things we will do is a clean up of our test database. Remember at the start of this
guide we discussed the importance of setting the RACK_ENV environment variable (`ENV['RACK_ENV'] = 'test'`) in our spec_helper.rb file. You can see now first hand why this is so.
The code below is deleting everything from our test database before we run any tests. And this is a good thing! We want to know exactly what
we are testing and the easiest way to do this is to know exactly what data we are testing against, no more and no less.

{% highlight sh %}
require 'sinatra_helper'

RSpec.describe 'IdeasController' do
  before(:all) do
    Idea.delete_all
  end

  after(:all) do
    Idea.delete_all
  end
{% endhighlight %}

Save the `ideas_controller_spec.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

## database.yml

At this point its easy to get confused between your test database, your development database and of course production database. Where are all these databases configured???
The answer, like most things, is easy when you know where to look.

In the previous guide **Rails Girls Sinatra App Guide** [Sinatra App Guide](/sinatra-app-guide) we created our database.yml file with the following details
{% highlight sh %}
---
sqlite: &sqlite
  host: 127.0.0.1
  adapter: sqlite3

development:
  <<: *sqlite
  database: db/development.sqlite3

production:
  <<: *sqlite
  database: db/production.sqlite3

**test:**
  **<<: *sqlite**
  **database: db/test.sqlite3**

{% endhighlight %}

## Controller Tests Continued

Lets continue with out controller spec. After our database clean up code enter the following.

{% highlight sh %}
let(:valid_params) {
    {
      idea: {
        name:        'name',
        description: 'description',
        picture:     Rack::Test::UploadedFile.new(__FILE__)
      }
    }
  }

let(:empty_params) { {} }
{% endhighlight %}


Lets explain the above code in a bit more detail. We use `let` to set values that our tests will use. Let defines a `memoized helper method`. This means that `valid_params` and
`empty_params` will be cached across multiple calls in the same example (in RSpec an example is one describe block!). Basically we are
making our tests very explicit, its easy for us to see at a glance what values are being tested.

Note that `let` is lazy-evaluated: that means it is not evaluated until the first time it is called. So values only get computed if they are going to be used.

Our set up is now done, we have used the `let` statement to define our params and we have deleted any data in our test database. Lets create our first controller test

{% highlight sh %}
describe 'get / and /ideas' do
    it 'allows access' do
      %w(/ /ideas).each do |path|
        get path
        expect(last_response).to be_ok
      end
    end
  end
{% endhighlight %}

Throughout out specs you will see calls to `get`, `put`, `delete` and `post` methods. They simulate a request to our app and we usually follow the request with an assertion against the resulting response (that's the expect bit above!).

It is Rack::Test that is giving us such easy access to these methods, we simply use them. Remember we added the `rack-test` gem to our Gemfile at the start of this guide.

Save the `ideas_controller_spec.rb` (`command ⌘ + s`(mac), `control + s`(windows and linux) or use the menu option `File -> Save`).

Commit the updated `ideas_controller_spec.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "controller tests"
git push origin master
{% endhighlight %}

## Additional Controller Tests

Lets add the following specs to give us more test coverage. Again we will explain the more *interesting* snippets at the end

{% highlight sh %}
describe 'get /new /ideas/new' do
    it 'allows access' do
      %w(/new /ideas/new).each do |path|
        get path
        expect(last_response).to be_ok
      end
    end
  end

  describe 'post /ideas' do
    context 'with invalid params' do
      it 'responds with an error' do
        post '/ideas', empty_params
        expect(last_response).to_not be_ok
        expect(last_response.status).to eq(500)
        expect(Idea.count).to eq(0)
      end
    end

    context 'with valid params' do
      after(:each) do
        Idea.delete_all
      end

      redirect '/ideas'

      it 'creates the post' do
        post '/ideas', valid_params
        expect(last_response.status).to eq(302)
        expect(Idea.count).to eq(1)
        expect(Idea.last.name).to eq(valid_params[:idea][:name])
        expect(Idea.last.description).to eq(valid_params[:idea][:description])
        expect(Idea.last.picture.include?(valid_params[:idea][:picture].original_filename)).to be_truthy

      end
    end

  end

  describe 'get /ideas/:id' do
    it 'allows access' do
      post '/ideas', valid_params
      get "/ideas/#{Idea.last.id}"
      expect(last_response).to be_ok
    end
  end

  describe 'get /ideas/:id/edit' do
    it 'allows access' do
      post '/ideas', valid_params
      get "/ideas/#{Idea.last.id}/edit"
      expect(last_response).to be_ok
    end
  end

  describe 'put /ideas/:id' do
    it 'allows access' do
      post '/ideas', valid_params
      put "/ideas/#{Idea.last.id}", valid_params
      expect(last_response.status).to eq(302)
    end
  end

  describe 'delete /ideas/:id' do
    it 'allows access' do
      post '/ideas', valid_params
      delete "/ideas/#{Idea.last.id}"
      expect(last_response.status).to eq(302)
    end
  end


end
{% endhighlight %}

Lets explain some parts. In our `context 'with valid params' do` we checked if the response status was 302. A response status of 302 is simply a redirect.
Remember back in our controller if the post was successfully saved we did this;

{% highlight sh %}
if @idea.save
   redirect '/ideas'
   ...
 {% endhighlight %}

In our `context 'with invalid params' do` we checked if the response status was 500.
This means the server encountered an unexpected condition (in this case invalid data) which prevented it from fulfilling the request (i.e saving the record).

The final interesting test code is
{% highlight sh %}
expect(Idea.last.picture.include?(valid_params[:idea][:picture].original_filename)).to be_truthy
{% endhighlight %}

The test passes if expectation is truthy (not nil or false). So what we are saying here is that we expect the picture attribute to pass if the value is anything but nil or false.
This gives our test more flexibity then using the `be_true` matcher which will test for an exact value.


Commit the updated `ideas_controller_spec.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "morecontroller tests"
git push origin master
{% endhighlight %}

# THE END
Congrats you have finished testing your sinatra app with RSpec. You can use this guide as a helper for your next set of tests. Its is
based on best practice contains a lot of explanations.

# Next Steps

Keep in touch!! We love to hear from you.

Follow us on twitter
**Sorcha [@sabel25](https://twitter.com/sabel25)**
**Rachelle [@rachelleonrails](https://twitter.com/rachelleonrails)**
**Rails Girls Brisbane [@railsgirlsbne](https://twitter.com/railsgirlsbne)**

Read the Rails Girls Brisbane blog [http://railsgirlsbrisbane.github.io/](http://railsgirlsbrisbane.github.io/)

