---
layout: default
title: Ruby Girls Cloud9 Sinatra App
permalink: sinatra-app-guide
---

# Ruby Girls Sinatra App Guide

*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## What is Sinatra?????
<img src="/images/sinatrawiki.png">


## Getting Started

## *1.*Creating the application with Cloud9

Follow the steps outlined in the Ruby Girls Cloud9 Sinatra Setup Guide [Could9 Setup](/cloud9-sinatra-guide)

## *2.*Locate the terminal window in Cloud9

Enter the following commands in the terminal window
{% highlight sh %}
gem install sinatra
gem install thin
gem install shotgun
{% endhighlight %}

The first gem `sinatra` allows us to use the sinatra DSL (Domain Specific Language) in our app

`Thin` is a fast web server

`Shotgun` is development server that reloads our app code on each request so we don't need to restart the server to see our changes

## *3.*Create a Gemfile

We need to manually create a Gemfile for our app. This can be done from the terminal window or the cloud9 file explorer window. In cloud9 you can right click and create new file/folder. In the examples that follow we use the terminal window
{% highlight sh %}
touch Gemfile
{% endhighlight %}
A Gemfile will now appear in the Cloud9 file explorer window. Double click to open it and input the following gems

{% highlight sh %}
source 'https://rubygems.org'

gem "sinatra"
gem "sqlite3"
gem "activerecord"
gem "sinatra-activerecord"
group :development do
    gem "shotgun"
    gem "tux"
end
{% endhighlight %}

Save the Gemfile (`cmd + s`) or menu bar `file -> save`.
Any time you edit a Gemfile you must run the following commant in the terminal window

{% highlight sh %}
bundle install
{% endhighlight %}

### Quick Overview of the gems

We have included three news gems to our project;

**sqlite3** will be the database for the app

**activerecord** is the interface the app will use to communicate with the database

**sinatra-activerecord** is a bridge that lets us use Active Record in a sinatra app

**tux** lets us interact with the db via the command line


## *4.*Project Structure

Sinatra doesn’t impose any structure on your project (which is both a blessing and a curse). The entire structure of the project is in your hands however it is this flexibility that can cause you headaches. The structure outlined in this ruby girls app is a pattern that can work well for many of your future sinatra apps.

The next few instructions will focus on file and folder creation. Pay particular attention to where you create files/folders. Ask an instructor if you are unsure

### *4a.* File/Folder Creation

This section will concentrate on creating files and folders via the terminal window. You can however also create all files and folders by right clicking in the cloud9 explorer window. If you feel comfortable with file and folder creation follow the structure in the image below and skip to section 5.

<img src="/images/sinatra_visual_tree.png">

From the terminal create a folder called `app`

{% highlight sh %}
mkdir app
{% endhighlight %}

and inside the app folder create 3 new subfolders

{% highlight sh %}
cd app
mkdir models views controllers
{% endhighlight %}

You have just create a big part of our apps structure

1. models (database communication)
2. views (what the end user sees)
3. controllers (lots of routes for our app)

From the terminal window you need to move up one level of the project structure and into the root of the project

{% highlight sh %}
cd ..
{% endhighlight %}

Next we will create a folder called config. Once done, we will create a file called application.rb inside the newly created config folder. This is the file that's going to load all the files our app needs

{% highlight sh %}
mkdir files
mkdir config
cd config
touch application.rb
touch database.yml
{% endhighlight %}

From the terminal window you need to move up one level of the project structure and into the root of the project. From here create another folder called db. This will store information about our sqlite3 database including our database migrations.
{% highlight sh %}
cd ..
mkdir db
{% endhighlight %}

We are almost there. The final step in our skeleton structure for our app is to create a config.ru file in the root of the project. The config.ru file is a convention that some deployment procedures and tools (like shotgun, tux, and Heroku) look for

{% highlight sh %}
touch config.ru
touch Rakefile
{% endhighlight %}

Congratulations you may not think it yet but you have just created a sound structure for a sinatra app that you can reuse time and time again

## *5.* Setup Coding

We are now ready to start some development work.

The first step in our sinatra application is setting up the application.rb file. Double click on the file to open it (you will find it under the config folder). Take some time to understand what we are about to put into this file.
Note you must save all files in cloud9 (`cmd + s` or `menu bar`)

{% highlight sh %}
require 'bundler'
Bundler.require
$: << File.expand_path('../', __FILE__)
Dir['./app/**/*.rb'].sort.each { |file| require file }
set :root, Dir['./app']
set :public_folder, Proc.new { File.join(root, 'assets') }
set :erb, :layout => :'layouts/application'
{% endhighlight %}

Lets examine the first four lines above in a bit more detail;

__require 'bundler'__ works to automatically discover the Gemfile

__Bundler.require__ then loads into the project all the gems you have specified in your Gemfile

__$: << File.expand_path('../', __FILE__)__ adds the whole project to $LOAD_PATH which really just means sinatra can find all the files you’ve added to this project

__Dir['./app/**/*.rb'].sort.each { |file| require file }__ here we are explicitly requiring each file in our model, views and controllers.

Even though we haven't set them up yet, we already know the project is going to need these files.
The last 3 lines of the application.rb set the root of the project and inform the app where the erb files and css files are located.

### *5a.* Rakefile

We need to set up our Rake file so we can run our helper tasks
{% highlight sh %}
require './config/application'
require 'sinatra/activerecord/rake'
{% endhighlight %}


from the terminal window run
{% highlight sh %}
bundle install
{% endhighlight %}

### *.5b* database.yml
The final step in our setup is this file. Open the config folder and double click to open the file. Copy and paste the lines below. It's our sqlite database setup.

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

test:
  <<: *sqlite
  database: db/test.sqlite3

{% endhighlight %}

## *6.* Migrations
From the terminal window run
{% highlight sh %}
rake db:create_migration NAME=create_ideas
{% endhighlight %}

A file will have been created in the db folder, double click to open it and enter the following. This is similar to the scaffold command you ran in rails girls

{% highlight sh %}
class CreateIdeas < ActiveRecord::Migration
  def change
    create_table :ideas do |t|
      t.string :name
      t.text :description
      t.string :picture

      t.timestamps null: false
    end
  end

  def down
    drop_table :ideas
  end
end
{% endhighlight %}

Everytime we create a migration we must run it. Open the terminal window and type

{% highlight sh %}
bundle exec rake db:migrate
{% endhighlight %}

## *7.* Project Code
We have just created our idea table by running the migration, so lets now create our corresponding model. Navigate to the app -> model folder and then create the model `idea.rb` file (right click in cloud9 file explorer window or use the terminal window). We will use the terminal window

{% highlight sh %}
touch idea.rb
{% endhighlight %}

Double click to open the file and input in the following code

{% highlight sh %}
class Idea < ActiveRecord::Base

end
{% endhighlight %}

## *8.* Routes
Finally its time to create routes. From the controllers folder create a file called `ideas_controller.rb` (you can use the terminal window or cloud9 file explore window)

{% highlight sh %}
  %w(/ /ideas).each do |path|
    get path do
      @ideas = Idea.all
      erb :'ideas/index'
    end
  end
{% endhighlight %}

Now that we have created our route we need to create a corresponding index page to display it. Create a folder for `ideas` in the apps/views/ directory (from terminal window `mkdir ideas` or use the cloud9 file explorer window). Then create a file called `index.erb`

### *8a.* Views
Type or paste the following into the index.erb file you just created

{% highlight sh %}
<h1>Listing Ideas</h1>

<table>
  <thead>
  <tr>
    <th>Name</th>
    <th>Description</th>
    <th>Picture</th>
    <th colspan="3"></th>
  </tr>
  </thead>

  <tbody>
  <% @ideas.each do |idea| %>
      <tr>
        <td><%= idea.name %></td>
        <td><%= idea.description %></td>
        <td><%= idea.picture.present? %></td>
        <td><a href="/ideas/<%= idea.id %>/edit">Edit</a></td>
        <td><a href="/ideas/<%= idea.id %>">Show</a></td>
        <td><% if idea.picture %><a href="/files/<%= idea.picture %>">View Picture</a><% end %></td>
        <td><% if idea.picture %><a href="/files/<%= idea.picture %>/download">Download Picture</a><% end %></td>
        <!--<td><a href=""<%#= delete_idea_button(idea.id) %>-->
      </tr>
  <% end %>
  </tbody>
</table>
<br>
{% endhighlight %}

## *9.* Improving our design

Next we should create an application.erb file where we can set our headers and footers and adhere to the DRY (Don't Repeat Yourself) principle of ruby programming

Create a subfolder of views and call it `layouts`. Then create a file in the layout folder and call it application.erb

Enter the following html/erb code
{% highlight sh %}
<!DOCTYPE html>
<html>
<head>
  <title>Workspace</title>
  <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
  <!-- Latest compiled and minified CSS -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
  <!-- Optional theme -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
  <!-- Application CSS -->
  <link rel="stylesheet" type="text/css" href="css/application.css">
  <!-- Application JS -->
  <script src="js/application.js"></script>
</head>
<body>
<nav class="navbar navbar-default navbar-fixed-top" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="/ideas">The Idea app</a>
    </div>
    <div class="collapse navbar-collapse">
      <ul class="nav navbar-nav">
        <li class="active"><a href="/ideas">Home</a></li>
        <li class="active"><a href="/new">New Idea</a></li>
        <li class="active"><a href="/about">About Me</a></li>
      </ul>
      <p class="navbar-text pull-right">

    </div>
  </div>
</nav>
<div class="container">

  <% @flash.each do |type, content| %>
    <% if content.respond_to?('each') %>
      <% content.each do |item| %>
        <%= render 'alert',
                   type:    type,
                   content: item %>
      <% end %>
    <% end %>
  <% end if @flash %>

  <%= yield %>
</div>
<footer>
  <div class="container">
    Ruby Girls 2016
  </div>
</footer>
<script src="//cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.0.3/js/bootstrap.js"></script>
</body>
</html>
{% endhighlight %}

### *9a.* Preview in a browser

Note you need to be back in the root of your project to start the server. Use `cd ..` from your terminal window continually until you return to your workspace. Don't worry if you go back to far, simply `cd workspace` will bring you back to where you should be. Ask an instructor if unsure.

From the terminal window run the following

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

From the cloud 9 ide click the share button on the right hand upper window. The select the copy option and paste the url into the browser window

<img src="/images/c9_sinatra/c9_browser.png">

You should now see the ideas app running in the browser. `ctrl+c` will terminate the server

## *10.* New Route and View

Generally speaking each route will have a corresponding view. Lets create a `new` idea route and view

Open the `ideas_controller.rb` file and insert the following

{% highlight sh %}
%w(/new /ideas/new).each do |path|
  get path do
    @title = 'New Idea'
    @idea  = Idea.new
    erb :'ideas/new'
  end
end
{% endhighlight %}

Once done we need to create the new erb/html page

Navigate to the views -> ideas folder and create a new file called `new.erb`

{% highlight sh %}
<h1><%=@title %></h1>
<form action="/ideas" method="post" enctype="multipart/form-data">
  <label for="idea_name">Name:</label><br />
  <input id="idea_name" name="idea[name]" type="text" value="<%= @idea.name %>" />
  <br />

  <label for="idea_description">Description:</label><br />
  <textarea id="idea_description" name="idea[description]" rows="5"><%= @idea.description %></textarea>
  <br />

  <label for="idea_picture">Picture:</label><br />
    <input type="file" id="idea_picture" name="idea[picture]" rows="5"><%= @idea.picture %>
  <br />

  <input type="submit" value="Create Idea" />
</form>

{% endhighlight %}

Lets take a moment to examine what we have just done. Our first step was to create a route for the `new` action. This route
is called when we want to create a new idea and save it to our database. As you can see from the controller `erb :'ideas/new'` will
render the `new.erb` page on the browser. From here the user will input the idea and click the submit button.


We now have to create a route that will know how to save the data to the database (which is what happens when you click the submit button).
Navigate to the `ideas_controller.rb` and enter the following route that will allow us to do just that.

{% highlight sh %}
post '/ideas' do
  if params[:idea]
    @idea         = Idea.new(params[:idea])
    if params[:idea][:picture] && params[:idea][:picture][:filename] && params[:idea][:picture][:tempfile]
      @filename     = params[:idea][:picture][:filename]
      @idea.picture = @filename
      file          = params[:idea][:picture][:tempfile]
      FileUtils.copy_file(file.path,"files/#{@idea.picture}")
    end
    if @idea.save
      redirect '/ideas'
    else
      erb :'ideas/new'
    end
  else
    erb :'ideas/new'
  end
end
{% endhighlight %}


### *10a.* Preview in a browser
From the terminal window run the following

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

From the cloud 9 ide click the share button on the right hand upper window. The select the copy option and paste the url into the browser window

<img src="/images/c9_sinatra/c9_browser.png">

You should now see the ideas app running in the browser. ctrl + c will terminate the server

## *11.* More routes

The next route in our application will allow us to retrieve just one idea from our database.

Navigate again to our `ideas_controller.rb` file and enter the following route

{% highlight sh %}

get '/ideas/:id' do
  @idea     = Idea.find(params[:id])
  erb :'ideas/show'
end
{% endhighlight %}

Similar to before this route is calling `erb :'ideas/show'` so lets create that file now. Navigate to the views -> ideas folder and create a file called `show.erb`

{% highlight sh %}
<p>
  <strong>Name:</strong>
  <%= @idea.name %>
</p>

<p>
  <strong>Description:</strong>
  <%= @idea.description %>
</p>

<p>
  <strong>Picture:</strong>
  <%= @idea.picture if @idea.picture.present? %>
</p>
<p> <%= delete_idea_button(@idea.id) %></p>
{% endhighlight %}


### *11a.* Delete routes

Lets add the code to delete an idea from our app

Navigate to the `ideas_controller.rb` and enter the following helper route

{% highlight sh %}
helpers do
  def delete_idea_button(idea_id)
    erb :'ideas/_delete_idea_button', locals: { idea_id: idea_id }
  end
end
{% endhighlight %}


Next we need to create the view that route calls (`ideas/_delete_idea_button`). Lets do that now. Navigate to the `views -> ideas` folder and create a new file called `_delete_idea_button.erb`. Be sure to include the underscore.

Open the newly created file and enter

{% highlight sh %}
<form action="/ideas/<%= idea_id %>" method="post">
  <input type="hidden" name="_method" value="delete" />
  <input type="submit" value="Delete Idea" />
</form>
{% endhighlight %}

Finally we need to create the route that will do the actual deleting. Return to the `ideas_controller.rb` file and enter the following

{% highlight sh %}
delete '/ideas/:id' do
  @idea = Idea.find(params[:id]).destroy
  redirect '/ideas'
end
{% endhighlight %}

Lets preview our changes in the browser. From the terminal window run the following

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

### *11b.* Updating that one Idea

We are almost done! Next we want to retrieve a single idea and update it. Lets create a new route.

{% highlight sh %}
get '/ideas/:id/edit' do
  @idea = Idea.find(params[:id])
  erb :'ideas/edit'
end
{% endhighlight %}

Again we see the route towards the end of the route it calls a view `erb :'ideas/edit'`. Lets create it, navigate to views -> ideas and create
a file called `edit.erb` with the following

{% highlight sh %}

<h1>Edit Post</h1>
<form action="/ideas/<%= @idea.id %>" method="post" enctype="multipart/form-data">
  <input type="hidden" name="_method" value="put" />

  <label for="idea_name">Name:</label><br />
  <input id="idea_description" name="idea[name]" type="text" value="<%= @idea.name %>" />
  <br />

  <label for="idea_description">Description:</label><br />
  <textarea id="idea_description" name="idea[description]" rows="5"><%= @idea.description %></textarea>
  <br />

  <label for="idea_picture">Picture:</label><br />
  <input type="file" id="idea_picture" name="idea[picture]" rows="5"><%= @idea.picture %>
  <br />

  <input type="submit" value="Update Idea" />
</form>
{% endhighlight %}

From our form action we know to create another route to update the database with the new data. Navigate back into the `ideas_controller.rb` and
input the following

{% highlight sh %}
put '/ideas/:id' do
  if params[:idea].try(:[], :picture)
    file      = params[:idea][:picture][:tempfile]
    @filename = params[:idea][:picture][:filename]
  end

  @idea = Idea.find(params[:id])

  if @idea.update_attributes(params[:idea])
    if @filename
      @idea.picture = @filename
      @idea.save
      File.open("./files/#{@filename}", 'wb') do |f|
        f.write(file.read)
      end
    end
    redirect "/ideas/#{@idea.id}"
  else
    erb :'ideas/edit'
  end
end

{% endhighlight %}

Lets preview our changes in the browser. From the terminal window run the following

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

We have left out an important route for our app, managing the files we uploaded. To keep things simple lets create a new controller that will deal with the files in our app

Navigate to the `apps -> controllers` view and create a new controller called `files_controller.rb`. Input the following routes

{% highlight sh %}
get '/files/:filename/download' do |filename|
  send_file "./files/#{filename}", :filename => filename, :type => 'Application/octet-stream'
end

get '/files/:filename' do |filename|
  send_file "./files/#{filename}"
end
{% endhighlight %}

Lets try to download or view our files in the browser. From the terminal window run the following

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

## *12.* CSS

Lets add some css to our app.

Using the terminal window `cd` into the app folder and then do the following (again you can use the cloud9 file explore window to create these folders)

{% highlight sh %}
cd app
mkdir assets
cd assets
mkdir css
touch application.css
{% endhighlight %}

Lets open our newly created `application.css` file and add the following

{% highlight sh %}

{% endhighlight %}

## *13.* Heroku

Following the heroku guide [Pushing to Heroku](/heroku)

## *14.* Github

Finally we should push all our code to Github.

Log onto Github and create a new repository

<img src="/images/github_new_repo.png">


Give your repo a title and a description


<img src="/images/github_create_repo.png">

Follow the instructions on Github under the heading `…or push an existing repository from the command line`

They will be similar to below. DO NOT copy the lines below. You must push to your own newly created repo

{% highlight sh %}
git remote add origin https://github.com/YourName/project_name.git
git push -u origin master
{% endhighlight %}


## *15.* The End

Every app should be accompanied by a README.md document. Edit this file with details about your app and push this update to Github (use the steps outlined above)

Congratulations you have just completed your first sinatra app!