---
layout: default
title: Rails Girls Next Testing Sinatra
permalink: sinatra-rspec-guide
---

# Rails Girls Next - Testing with RSpec
*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## Unit Tests
Unit testing is the practice of testing each component of your program. We do this by defining inputs to the component and then checking the outputs. When our unit tests pass, and produce the expected output, we can have confidence that our code actually works.

They also allow you to refactor with confidence, running tests (specs) that pass before and after refactorings helps prove your code still works as expected.

As our application gets more complex, and we write more code to add more functionality, the number of unit tests required also grows. Running our tests manually could easily consume our every waking hour!

Thankfully with Ruby we have testing frameworks that allow us to automate these tests using code.

## What is RSpec?

RSpec is a framework that provides a powerful domain specific language (DSL) that allows us to automate our unit tests. What is a DSL? Well, in this context, it means that RSpec has additional language features on top of Ruby to create the most efficient language for testing your code.

In this guide we will be creating unit tests for our Idea model and controller. RSpec refers to tests as 'specs' (short for specifications) so we will adopt that terminology moving forward.

The specs we will write for our model will concentrate primarily on validation. For our controller testing we will simulate requests made to the app and specify expectations about what the response should be.

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

**rspec** is a testing framework and supports a powerful DSL (Domain Specific Language) that allows us to unit test our apps.

**rack-test** includes helper methods that allow us to simulate requests against our app and assert expectations about the response.

## *1b.* Installing rspec

Run the following command in the terminal window:

{% highlight sh %}
bundle exec rspec --init
{% endhighlight %}

Running this command will create a `spec` folder with two new files:
1. `.rspec`
2. `spec_helper.rb`

## *1c.* Adding validation to our model

Before we begin writing our new specs let's first create a validation for our Idea model.

When we create a new Idea, it should have a name. An idea without a name simply doesn't make sense! So let's enforce this rule by coding it directly into the model.

Navigate to the app/models/idea.rb file and add `validates_presence_of :name` so that your file should look like the example below:

{% highlight sh %}
class Idea < ActiveRecord::Base
  validates_presence_of :name
end
{% endhighlight %}

Save the `idea.rb` file.

Commit the updated `idea.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "updated Gemfile"
git push origin master
{% endhighlight %}

We are now ready to start testing our app!

# Setting up our spec_helper file

```
NOTE: Come back to this: do we need to run something equivalent to
rails generate rspec:install
in order for
"the newly-created `spec_helper.rb` file"
to be true?
```

Navigate to the newly-created `spec_helper.rb` file inside the spec folder and type the following at the start of the file.
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

Save the `spec_helper.rb` file.

Lets take a moment to understand what we have just typed...

The first line; `require_relative '../config/application’` is including your entire sinatra app into the test suite! Without it, the specs would not be able to find the code they need to execute. Take another look at the config/application.rb file we created as part of our sinatra app earlier, it requires the whole project as well.

The next two lines require the gems we added to our Gemfile in the previous step.

The following line (`ENV['RACK_ENV'] = 'test'`) sets the RACK_ENV environment variable, which Sinatra checks when setting its environment. This is a very important step as it ensures the specs all run against your test database (and not development one).

Take a moment to absorb this, imagine what would happen if every time we ran our specs they ran against your development database? In general a test environment will mimic production and therefore will contain a lot of important data.

In the specs we are about to create we delete all records from our test database as a clean up.

The next step (from `module RSpecMixin` to `end`) is to create a module that will include the Rack::Test methods that RSpec can use. The Rack::Test::Methods module includes a variety of helper methods that allow us to simulate requests against the application (we defined the app inside the module) and assert expectations about the response.

Finally we are configuring RSpec to include the module we just created, thereby ensuring our specs have access to all the helper methods.

Configuring `profile_examples` means that RSpec will inform you of your slowest specs. Its good practise to use this, slow specs can be an indication of slow production performance.

# Spec Folder Structure

Earlier we added validation to our model and specified that for an idea record to be valid record it must have a name. Let's test that now.

Create a new file inside the spec folder called sinatra_helper.rb. Open it and type the following:
{% highlight sh %}
require 'spec_helper'
{% endhighlight %}

Save the `sinatra_helper.rb` file.

We are now ready to create some specs!

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
Using the terminal navigate into the new model folder and create a new file called `idea_spec.rb` using the following commands:
{% highlight sh %}
cd models
touch idea_spec.rb
{% endhighlight %}

Open the newly created `idea_spec.rb` file and enter the following code:

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

Save the `idea_spec.rb` file.

A few points to note;

We required the `sinatra_helper` file. This is the file with all the setup we created above.

Also in the first `RSpec.describe` block we are referencing the Idea class name. When we use a Class name in the describe block, we can make use of the built-in `described_class` method. In this case `described_class` refers to the Idea model class.

We have created two explicit specs, one with a name and one without. As we added validation to our model earlier, we can expect the test with a name to be valid and a test without a name to not be valid.

Take some time to get familiar with the specs you have just added. Ask your mentors for help.

You can see we are explicitly setting model values. It is considered by many best practise to be as explicit as possible in your specs. In fact it's the one place in Ruby where the DRY principle doesn't apply.

## Run the test
**NB always run your specs from the root of your project and not from within the spec folder**

Using the terminal type the following
{% highlight sh %}
cd ~/workspace/
bundle exec rspec spec/app/models/idea_spec.rb
{% endhighlight %}

The output of the specs should resemble the output below:

<img src="/images/model_passing_test.png">

## Committing to git
{% highlight sh %}
git add .
git commit -m "model specs"
git push origin master
{% endhighlight %}

# Testing the Controller

So far we have tested model validation. While that is very important, we still have much more to test in order to get good 'test coverage'.

From your terminal window navigate to the `spec/app/controllers` directory and create an `ideas_controller_spec.rb` file:

{% highlight sh %}
cd ~/workspace/spec/app/controllers/
touch ideas_controller_spec.rb
{% endhighlight %}

Open the newly created file so we can start writing our specs.

One of the first things we will do is a clean-up of our test database. Remember at the start of this guide we discussed the importance of setting the RACK_ENV environment variable (`ENV['RACK_ENV'] = 'test'`) in our spec_helper.rb file? You can see now first hand why this is so.
The code below is deleting everything from our test database before we run any specs. This is a good thing! We want to know exactly what
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

Save the `ideas_controller_spec.rb` file.

## database.yml

At this point its easy to get confused between your test database, your development database and production database. Where are all these databases configured? The answer, like most things, is easy when you know where to look.

In the previous guide **Rails Girls Sinatra App Guide** [Sinatra App Guide](/sinatra-app-guide) we created a file called `database.yml` with the following details:

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

This is where you go if you need to configure any of your databases.

## Controller Tests Continued

Let's continue with our controller spec. Add the following code immediately after the database clean up code we added above:

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

Let's explain the above code in a bit more detail.

We use `let` to set variables that our specs will use. Technically speaking, `let` is what is known as a `memoized helper method`. This simply means that the two parameters we just defined, `valid_params` and `empty_params`, will be evaluated once and that value is made available throughout the block in which it was called. If it is used again in another block, it will be evaluated again (as the value may be different with the different scenario).

Note that `let` is only evaluated the first time it is called (known as lazy-evaluated). So values only get computed if they are going to be used.

So what have we done so far?

We have completed our set up, we have deleted any data in our test database, and we have used the `let` statement to define our variables. Let's now create our first controller test. Add the following code to the `ideas_controller_spec` that you created above:

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

Throughout all our specs you will see calls to `get`, `put`, `delete` and `post` methods. They simulate a request from the browser to our app, and we usually follow the request with an assertion about the resulting response (that's the expect bit above!).

It is Rack::Test that is giving us access to these methods. (Remember we added the `rack-test` gem to our Gemfile at the start of this guide.)

Save the `ideas_controller_spec.rb` file.

Commit the updated `ideas_controller_spec.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "controller specs"
git push origin master
{% endhighlight %}

## Additional Controller Tests

Lets add the following specs to give us more test coverage. Again we will explain the more *interesting* snippets at the end.

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

Let's explain some parts.

You will notice the code is grouped into chunks each beginning with the word `describe`. Each chunk of code represents a single spec and is referred to as an example.

In the block of code beginning with the line `context 'with valid params' do`, we check to see if the response status is 302. A response status of 302 is simply a redirect.

Remember back in our controller if the post was successfully saved we did this;

{% highlight sh %}
if @idea.save
   redirect '/ideas'
   ...
 {% endhighlight %}

In our `context 'with invalid params' do` we checked if the response status was 500.
This means the server encountered an unexpected condition (in this case invalid data) which prevented it from fulfilling the request (i.e saving the record).

The final interesting piece of code is the following:
{% highlight sh %}
expect(Idea.last.picture.include?(valid_params[:idea][:picture].original_filename)).to be_truthy
{% endhighlight %}

The test passes if expectation is truthy. It might sound strange but it's really not. In Ruby `false` and `nil` are often regarded as having the boolean value of `false`. And everything else is regarded as having the boolean value of `true`.

The matcher you might expect to use, `be_true`, would mean that the expression above MUST evaluate to `true` and nothing else. If it evaluates to, say `1`, it will fail. What you actually want is to state that it should not be false. So you use `be_truthy` instead. This will pass if the expression evaluates to anything except for `nil` or `false`.

Still confused? Don't worry, it can take a while, we found it confusing as well. In fact, we even wrote a blog post about it. Check it out [here](http://railsgirlsbrisbane.github.io/rspec_matchers_blog).

Commit the updated `ideas_controller_spec.rb` to git:

{% highlight sh %}
git add Gemfile
git commit -m "more controller specs"
git push origin master
{% endhighlight %}

## THE END
Congratulations you have finished testing your sinatra app with RSpec! You can use this guide as a helper for your next set of specs.

## Next Steps

Keep in touch!! We love to hear from you.

# Keep in touch

Follow us on twitter

Sorcha <a href="https://twitter.com/sabel25" class="twitter-follow-button" data-show-count="false">Follow @sabel25</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rachelle <a href="https://twitter.com/RachelleOnRails" class="twitter-follow-button" data-show-count="false">Follow @RachelleOnRails</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rails Girls Brisbane <a href="https://twitter.com/railsgirlsbne" class="twitter-follow-button" data-show-count="false">Follow @railsgirlsbne</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Read the Rails Girls Brisbane blog [http://railsgirlsbrisbane.github.io/](http://railsgirlsbrisbane.github.io/)

Email us: <a href="mailto:railsgirlsbne@gmail.com?Subject=Hello%20RailsGirls" target="_top">Rails Girls Brisbane</a>

Join our Facebook Group: <a href="https://www.facebook.com/groups/462831463794656/">Rails Girls BNE Group</a>
