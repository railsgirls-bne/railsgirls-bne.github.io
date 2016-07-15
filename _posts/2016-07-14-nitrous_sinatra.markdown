---
layout: default
title: Ruby Girls Cloud9 Sinatra Setup Guide
permalink: nitrous-sinatra-guide
---

# Ruby Girls Nitrous Sinatra Setup Guide

*Created by Sorcha Abel <a href="https://twitter.com/sabel25" class="twitter-follow-button" data-show-count="false">Follow @sabel25</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script> and Rachelle LeQuesne <a href="https://twitter.com/RachelleOnRails" class="twitter-follow-button" data-show-count="false">Follow @RachelleOnRails</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Sign up for a [Github](https://github.com/) account.

Then, go to the [nitrous website](https://nitrous.io/).

Click the __signup__ button. Signup using the github account you just created.

Note the screenshots below were taken in December 2015. The website may have been updated since. Please ask an instructor if you are unsure.

<img src="/images/c9/c9-signup-with-github.png">

Once you have signed up, you will be shown your dashboard. __Create a New workspace__ by clicking the plus icon.

<img src="/images/c9_sinatra/c9_new_workspace1.png">

Name your new workspace _ruby_girls_, and make sure you are creating a custom workspace. Then click 'Create workspace'.

<img src="/images/c9_sinatra/c9_create_workspace2.png">

You will then be taken directly to your new workspace.

# Workspace environment

<img src="/images/c9_sinatra/c9_workspace_final.png">

The workspace is made up of a few important areas.

- The file explorer and editor are where we will be spending a lot of time. Choose files to edit using the explorer. They will open in a new tab in the file editor.
- The Terminal is used to enter commands to our application.

## Set up the text editor

The Ruby global convention is to use 2 spaces for indentation. Lets change the default editor to use 2 spaces.

Click on the settings icon in the upper right side of the window. A preferences window will open edit the __Soft Tabs__ setting to reduce it to 2.

<img src="/images/c9_sinatra/c9_settings4.png">

# Next Steps

You are now ready to start building your sinatra app but first a few tips.

1. You must save your work each time you make edits. Do this by using either `file -> save` from the menubar or `ctrl+s`(Windows and Linux), `command ⌘+s`(Mac)
2. To stop the sinatra server use `ctrl+c`
3. To run the sinatra server type the following into the terminal window.

{% highlight sh %}
ruby config/application.rb -p $PORT -o $IP
{% endhighlight %}

These will be covered in the next guide, click to open it [sinatra app guide](/sinatra-app-guide)

# Keep in touch

Follow us on twitter

Sorcha <a href="https://twitter.com/sabel25" class="twitter-follow-button" data-show-count="false">Follow @sabel25</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rachelle <a href="https://twitter.com/RachelleOnRails" class="twitter-follow-button" data-show-count="false">Follow @RachelleOnRails</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Rails Girls Brisbane <a href="https://twitter.com/railsgirlsbne" class="twitter-follow-button" data-show-count="false">Follow @railsgirlsbne</a> <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

Read the Rails Girls Brisbane blog [http://railsgirlsbrisbane.github.io/](http://railsgirlsbrisbane.github.io/)

Email us: <a href="mailto:railsgirlsbne@gmail.com?Subject=Hello%20RailsGirls" target="_top">Rails Girls Brisbane</a>

Join our Facebook Group: <a href="https://www.facebook.com/groups/462831463794656/">Rails Girls BNE Group</a>
