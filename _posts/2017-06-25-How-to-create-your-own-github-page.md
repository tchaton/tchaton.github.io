---
layout: post
title:  "How to set up your own blog using github pages and Jekyll"
date:   2017-06-25 12:00:00 +0200
categories: blog update
comments: true
---

Hey there,

Today I'd like to share with you some experience about setting up your own blog with Jekyll and Disqus on github pages.

Why using Jekyll and what is it ?
---

Jekyll is a tool that helps you create a blog, only using static files, it is incredibly simple using it (once you managed to get it up and running).
You can post articles easily by writing them first in markdown or html and simply putting them in your posts folder, Jekyll takes care of the rest ! It is easily customizable as 
well, there are many themes created by a large community. There is also a good integration for github pages, which is an awesome way of hosting your own *free* (static) website.
I'm using it myself for posting these articles. When coupling it with Disqus, a javascript tool that is easily embedded to your site, you almost have a dynamic blog, 
as it is possible for people to comment your articles.

So how do you set things up ?
---

Probably the easiest way to create your own blog is to fork a jekyll theme on github. I used [Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll). It's very well described how to get it up qnd running and voilà, there you got your own blog ! You can modify the pages easily as they are written in markdown. if you want to post your own articles, simply add them to the _posts folder, respecting the name format for the file. But everything is already described on the readme.

But if you want to make your site a little bit more personal ?
---

### Setting up Jekyll to run locally on your machine

I'm using windows, and set up jekyll using Bash on Ubuntu on Windows, a really awesome feature they added on windows 10 allowing you to use bash command lines just like you would on ubuntu !
It is really easy to install it, just go to the windows site explaining how to set it up.
Once you have bash installed on your windows, you need to install a few packages, just follow the tutorial on jekyll's page, but if you want to use github pages take care which version of Jekyll you are installing ! Installing version 3.5.0 while github pages only supports jekyll 3.4.3 will make everything you try to build fail. [Here is a link to the dependency versions you should install](https://pages.github.com/versions/).


Once you have all packages installed (ruby and so on) you are ready to launch your project.
In order to start your project just cd into the directory you want your project to be in and type:
	jekyll new name-of-your-project

This will create a jekyll template, using the minima theme, making it easy to get up and running in a short period of time. You can post things by putting files in your _posts folder.
The name of the posts file should be YEAR-MONTH-DAY-title.MARKUP. For example:

2017-06-22-how-to-write-a-blog.md

Each post, or even more generally every markdown file you'll want to publish to your site contains at the beginning what is called the frontmatter, where you can set various variables for your post, for example the title, subtitle, date..

Here is the frontmatter for the current article:

	---
	layout: post
	title:  "How to set up your own blog using github pages and Jekyll"
	date:   2017-06-24 12:45:00 +0200
	categories: blog update
	comments: true
	---

You can see what your site looks like if you run:

	bundle exec jekyll serve

If that command doesn't work, remove what's in your gemfile.

### Linking your site to github pages

If you want to launch your page on github pages you need to add a repository on github with the name username.github.io, my username is cernewein so I created 
a repository called cernewein.github.io

Now you need to link your local jekyll directory to this github directory. With command line cd into your project.

Type (make sure you have git installed on your PC):

	git init
	git remote add origin https://github.com/user/username.github.io.git

That's it for linking it to git.

Now you need to tell jekyll that you will be deploying your site to github pages. But before doing that there's a dependency we need to install.
Type:

	gem install bundle

Once this is done open you Gemfile in your jekyll project
Remove everything and replace it with the lines:

	source 'https://rubygems.org'
	gem 'github-pages'

Once this is done, run in your jekyll directory:

	bundle install

#### Some issues I encountered while setting things up

This is where it get's tricky, I had some issues trying to run this command.
My biggest issue was when trying to install *nokogiri*
I had missing packages that I didn't know were missing because it wasn't detailed in the error log.
So if you have an issue with nokogiri, install those packages first: libxml [Here is a stack overflow explaining the issue I faced](https://stackoverflow.com/questions/6277456/nokogiri-installation-fails-libxml2-is-missing)

Once this is done you can install nokogiri by running

	gem install nokogiri -- --use-system-libraries

You can then rerun bundle install and it should work.

Now it should be possible for you to deploy to github !

If you want to deploy on github simply push your jekyll project to your github repository

### Now how do you install Disqus ?

First of all you need to setup an account at disqus, and choose to host discussions. You then choose a unique identifier which will be used by jekyll to connect to your Disqus.

When running the jekyll new command, jekyll already creates basic templates for using basic jekyll. In order to get Disqus up and running I recommend you get the folders
_layouts
_includes from the minima github project. These already contain code in order to include Disqus.

In your posts.html file add the javascript code snippet you get from Disqus between the tags (remove the include at the end of your file):

	{% if comments!=false %}
	{% endif %}

Now in the posts you want to have comments on, in you frontmatter put the line:

	comments: true

That's it, it should be working for you !

If your disqus is running locally but not when deloying it to github, remove the this.site.url and this.site... variables, I know it is recommended to use them, but I simply
wasn't able to get disqus running on github with them..

That's it, I hope that this was helpful.

See you,

Cédric