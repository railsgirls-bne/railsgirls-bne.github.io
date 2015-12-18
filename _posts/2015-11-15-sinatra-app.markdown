---
layout: default
title: Ruby Girls Cloud9 Sinatra App
permalink: sinatra-app-guide
---

# Ruby Girls Sinatra App Guide

*Created by Sorcha Abel, [@sabel25](https://twitter.com/sabel25)*

## What is Sinatra?
<img src="/images/sinatrawiki.png">


# Getting Started

## *1.*Creating the application with Cloud9

Follow the steps outlined in the Ruby Girls Cloud9 Sinatra Setup Guide [Could9 Setup](/cloud9-sinatra-guide)

## *2.*Locate the terminal window in Cloud9

There are three gems we will need to install before we can create a Sinatra project.

To install these gems, enter the following commands in the terminal window:
{% highlight sh %}
gem install sinatra
gem install thin
gem install shotgun
{% endhighlight %}

The first gem `sinatra` allows us to use the Sinatra DSL (Domain Specific Language) in our app.

`Thin` is a lightweight and fast web server whose job is to serve up web pages as they are requested.

`Shotgun` is development server that reloads our application code with each request. This means we don't need to restart the server in order to see our changes.

## *3.*Create a Gemfile

We need to manually create a Gemfile for our app. This can be done from the terminal window or the Cloud9 file explorer window.

To use the Cloud9 explorer window, right mouse-click and select `New file`. Rename this file from `Untitled` to `Gemfile`.

In the examples that follow we use the terminal window.
{% highlight sh %}
touch Gemfile
{% endhighlight %}
A Gemfile will now appear in the Cloud9 file explorer window. Double click to open it and type in the following code which specifies which gems we want included in our application:

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

Save the Gemfile (`cmd + s` or menu options `file -> save`).

Any time you edit a Gemfile you must install the new gems. To do so, run the following command in the terminal window:

{% highlight sh %}
bundle install
{% endhighlight %}

### Quick Overview of the gems

We have included three news gems to our project:

**sqlite3** is the database we will be using for this application.

**activerecord** is the interface the application uses to communicate with the database.

**Sinatra-activerecord** is a bridge that allows us use Active Record in a Sinatra application.

**tux** allows us to interact with the database through the command line.


## *4.*Project Structure

Sinatra doesn’t impose any structure on your project (which can be both a blessing and a curse). The entire structure of the project is in your hands and this flexibility can cause headaches. The structure outlined in this app is a pattern that can work well for many of your future Sinatra apps.

In the next section we will focus on file and folder creation. Pay particular attention to where you create files/folders. Ask an instructor if you are unsure.

### *4a.* File/Folder Creation

The instructions given will show you how to create files and folders through the terminal window. You can however create all files and folders by right clicking in the Cloud9 explorer window as we did above. If you feel comfortable with file and folder creation follow the structure in the image below and skip to section 5.

From the terminal create a folder called `app` by executing the following command:

{% highlight sh %}
mkdir app
{% endhighlight %}

Change to the `app` folder and create three subfolders:

{% highlight sh %}
cd app
mkdir models views controllers
{% endhighlight %}

You have just create a major component of our application's structure:

1. models (database communication)
2. views (what the end user sees)
3. controllers (lots of routes for our application)

From the terminal window, move up one level of the project structure and into the root of the project:

{% highlight sh %}
cd ..
{% endhighlight %}

Frist we will create a folder called `files`. This folder will contain lots of files. `*** Come back to this and add detail ***` Next we will create a folder called `config`. Then we will create a file called `application.rb` inside the newly created `config` folder. `application.rb` is the file that will load all the files needed by our application. Finally we will create a file called `database.yml` that will manage our database connections.

{% highlight sh %}
mkdir files
mkdir config
cd config
touch application.rb
touch database.yml
{% endhighlight %}

From the terminal window, move up one level of the project structure and into the root of the project. From here create another folder called `db`. This will store information about our sqlite3 database including our database migrations.
{% highlight sh %}
cd ..
mkdir db
{% endhighlight %}

We are almost there. The final step in our skeleton structure for our application is to create a `config.ru` file in the root of the project. The `config.ru` file is a convention that is required by certain deployment procedures and tools (like shotgun, tux, and Heroku).

{% highlight sh %}
touch config.ru
touch Rakefile
{% endhighlight %}

Congratulations! You may not realise it yet but you have just created a solid basic structure for a Sinatra app that you can reuse time and time again.

By the time our application is complete, it should look similar to the folder structure below:

<img src="/images/sinatra_visual_tree.png">

## *5.* Setup Coding

We are now ready to begin developing our application.

The first step is to set up the `application.rb` file. Double click on the file to open it (you will find it under the `config` folder). Take some time to understand what we are about to put into this file.
Note you must save all files in cloud9 (`cmd + s` or menu options `file -> save`)

{% highlight sh %}
require 'bundler'
Bundler.require
$: << File.expand_path('../', __FILE__)
Dir['./app/**/*.rb'].sort.each { |file| require file }
set :root, Dir['./app']
set :public_folder, Proc.new { File.join(root, 'assets') }
set :erb, :layout => :'layouts/application'
{% endhighlight %}

Lets examine the first four lines in a bit more detail:

**require 'bundler'** enables our application to automatically discover the Gemfile.

**Bundler.require** loads into the project all the gems that are specified in the Gemfile.

**$: << File.expand_path('../', __FILE__)** adds the entire project to $LOAD_PATH. This allows Sinatra to find all the files you’ve added to the project.

**Dir['./app/**/*.rb'].sort.each { |file| require file }** This line explicitly requires each file found in our model, view and controller folders.

Even though we haven't set them up yet, we know the project is going to need these files.
The last three lines of `application.rb` sets the root of the project and tells the application where the erb (embedded Ruby) files and CSS files are located.

### *5a.* Rakefile

We need to set up our Rake file so we can run our helper tasks. Open the file `Rakefile` and add the following code:
{% highlight sh %}
require './config/application'
require 'sinatra/activerecord/rake'
{% endhighlight %}


Then to make those files available in the current environment, navigate to the terminal window and run:
{% highlight sh %}
bundle install
{% endhighlight %}

### *5b.* database.yml
The final step in our setup is the `database.yml` file. This file will contain our sqlite database setup and is required for connecting to the database. Open the config folder and double click to open the file. Copy and paste the lines below.

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

Just like we did in the original Rails Girls, we need to create an `ideas` table so we can save our ideas. From the terminal window run
{% highlight sh %}
rake db:create_migration NAME=create_ideas
{% endhighlight %}

A migration file will have been created in the `db/migrate` folder. It will be called `create_ideas.rb` and be prefixed with a unique id including the date and time. Double-click to open it and enter the following. This is similar to the scaffold command you ran in Rails Girls.

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

Each time we create a migration, it needs to be run so that the changes get applied to the database. Open the terminal window and type:

{% highlight sh %}
bundle exec rake db:migrate
{% endhighlight %}

This will create the `ideas` table. You can see it in the schema file, `db/schema.rb`.

## *7.* Project Code
We have just created the ideas table by running the migration, so let's now create the corresponding model. Navigate to the `app/model` folder and create the `idea.rb` file (right click in cloud9 file explorer window or use the terminal window). We will use the terminal window.

{% highlight sh %}
touch idea.rb
{% endhighlight %}

Double click to open the file and enter the following code:

{% highlight sh %}
class Idea < ActiveRecord::Base

end
{% endhighlight %}

This is just an empty file at this stage, we will add code to it later on.

## *8.* Routes
Finally it is time to create routes. Routes are simply the rules that tell your application which code to run for a given URL. From the `controllers` folder, create a file called `ideas_controller.rb` (you can use the terminal window or cloud9 file explore window)

{% highlight sh %}
  %w(/ /ideas).each do |path|
    get path do
      @ideas = Idea.all
      erb :'ideas/index'
    end
  end
{% endhighlight %}

Now that we have created our route we need to create a corresponding index page to display it. Create a folder for `ideas` in the `apps/views/` directory (from terminal window `mkdir ideas` or use the cloud9 file explorer window). Then create a file called `index.erb`.

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

Although our application currently has only one page, it will eventually have many pages. To give it a consistent look and feel, we will want to have the same header and footer on each page. Instead of copying and pasting the header and footer onto each page, and having to edit each page if we want to make a change, we will create one file that will contain all the common page content. This is an example of adhering to the DRY (Don't Repeat Yourself) principle of Ruby programming.

Create a subfolder of `views` folder and call it `layouts`. Then create a file in the `layout` folder and call it `application.erb`.

Enter the following html/erb code:
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

In order to start the server, you need to be in the root directory of your project. Use `cd ..` repeatedly from your terminal window until you return to your workspace. Don't worry if you go back too far, simply `cd workspace` will bring you back to where you should be. Ask an instructor if unsure.

From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

From the cloud 9 IDE click the `share` button on the right hand upper window. The select the `copy` option and paste the url into the browser window.

<img src="/images/c9_sinatra/c9_browser.png">

You should now see the ideas app running in the browser! `ctrl+c` will terminate the server.

## *10.* New Route and View

Generally speaking each route will have a corresponding view. Lets create a route and a view for when we want to create a new idea.

Open the `ideas_controller.rb` file and insert the following:

{% highlight sh %}
%w(/new /ideas/new).each do |path|
  get path do
    @title = 'New Idea'
    @idea  = Idea.new
    erb :'ideas/new'
  end
end
{% endhighlight %}

Now we need to create the HTML page to allow our users to add a new idea.

Navigate to the `views/ideas` folder and create a new file called `new.erb`. Add the following code:

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

Let's pause for a moment to review what we have just done. Our first step was to create a route for the `new` action. We did that in the `ideas_controller.rb` file.

First, we set up two variables that will be used by the `new` HTML page, `@title` and `@idea`. `@title` is just the text that appears at the top of the web page. `@idea` is a variable that will hold all the data we will enter into our HTML page and eventually insert in to the database.

Finally, with the line `erb :'ideas/new'` we defined a route that will link the URL `ideas/new` to the "New Idea" web page.

So we have a route that will allow users to enter a new idea but we have no way yet of allowing them to save that idea to the database. If you add the following code to `ideas_controller.rb`, we will be able to do just that.

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
From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

From the Cloud 9 IDE click the `share` button on the right hand upper window. The select the `copy` option and paste the URL into the browser window.

<img src="/images/c9_sinatra/c9_browser.png">

You should now see the ideas app running in the browser. ctrl + c will terminate the server.

## *11.* More routes

So now we can add an idea in our app and save it to the database. But what about retrieving an idea from the database? The next route in our application will allow us to retrieve just one idea from our database.

Navigate to `ideas_controller.rb` and enter the following route:

{% highlight sh %}

get '/ideas/:id' do
  @idea = Idea.find(params[:id])
  erb :'ideas/show'
end
{% endhighlight %}

Similar to before this route is calling `erb :'ideas/show'` so let's create that file now. Navigate to the `views/ideas` folder and create a file called `show.erb`. Add the following code:

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

But what if we want to delete an idea that we have previously added?

Navigate to `ideas_controller.rb` and enter the following code:

{% highlight sh %}
helpers do
  def delete_idea_button(idea_id)
    erb :'ideas/_delete_idea_button', locals: { idea_id: idea_id }
  end
end
{% endhighlight %}

This is called a helper route. It is referring to a view called `ideas/_delete_idea_button` so let's create that.

Navigate to the `views/ideas` folder and create a new file called `_delete_idea_button.erb`. Be sure to include the underscore.

Open the newly created file and enter the following code:

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

Lets preview our changes in the browser. From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

### *11b.* Updating that one Idea

We are almost done! The final piece of database functionality it to retrieve a single idea and update it. As we did in the previous steps, let's start by creating a new route. Add the following code to `ideas_controller.rb`:

{% highlight sh %}
get '/ideas/:id/edit' do
  @idea = Idea.find(params[:id])
  erb :'ideas/edit'
end
{% endhighlight %}

Again we see the route is calling a view `erb :'ideas/edit'`. Lets create it by navigating to `views/ideas` and creating
a file called `edit.erb` with the following code:

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

As with the `new` action above, we know we will need to create a second route to update the database with the new data. Navigate back into `ideas_controller.rb` and add the following code:

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

Lets preview our changes in the browser. From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

We have left out an important route for our app: managing the files we uploaded. To keep things simple let's create a new controller that will deal with the files in our app.

Navigate to the `apps/controllers` folder and create a new controller called `files_controller.rb`. Add the following code to create the required routes:

{% highlight sh %}
get '/files/:filename/download' do |filename|
  send_file "./files/#{filename}", :filename => filename, :type => 'Application/octet-stream'
end

get '/files/:filename' do |filename|
  send_file "./files/#{filename}"
end
{% endhighlight %}

Let's try to download or view our files in the browser. From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

## *12.* CSS

Lets add some css to our app.

Using the terminal window `cd` into the app folder and then do the following (again you can use the Cloud9 file explore window to create these folders)

{% highlight sh %}
cd app
mkdir assets
cd assets
mkdir css
touch application.css
{% endhighlight %}

Lets open our newly created `application.css` file and add the following code:

{% highlight sh %}
body { padding-top: 100px; }
footer { margin-top: 100px; }
table, td, th { vertical-align: middle; border: none; color: brown; }
th { border-bottom: 1px solid #DDD; }
{% endhighlight %}

Let's preview our changes in the browser. From the terminal window run the following to start to start the server:

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
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

Congratulations you have just completed your first Sinatra app!