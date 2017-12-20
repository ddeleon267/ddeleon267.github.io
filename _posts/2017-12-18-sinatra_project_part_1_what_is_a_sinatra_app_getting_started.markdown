---
layout: post
title:      "Sinatra Project Part 1: What is a Sinatra App? + Getting Started"
date:       2017-12-18 14:32:04 -0500
permalink:  sinatra_project_part_1_what_is_a_sinatra_app_getting_started
---

(Note that this is a VERY rough draft and only serves as an initial blog post for my Sinatra Project. In particular, it is a brief intro to Sinatra + a VERY basic overview of the design stages for my project. There will be subsequent posts that 1) go through my app in more detail and 2) reflect on my project and process as well as explore difficulties and future considerations. Links to my project and video walkthrough will also be added later)


## Intro

This is intended to be a brief introduction to my Sinatra App with a little background for those who are unfamilliar with or new to Sinatra applications

This is my second major portfolio project, and the requirements are pretty simple. The app must:

* Be built in Sinatra using MVC conventions and full CRUD functionality
* Have multiple models and > 1 has_many relationship
* Have user accounts with validations for signup
* Include safety features, such as validations, that keep users who aren’t you from editing your content!

**Sinatra**

Sinatra is a lightweight Ruby framework (compared to the big guns you get with Rails) that is used to make dynamic web applications. The Sinatra community seems fun and sassy as well, and the [documentation](http://sinatrarb.com/documentation.html) is easy to read and digest (compared to, ahem, Rails documentation). 

**MVC**
MVC, or Model-View-Controller, is a programming design paradigm/pattern that designates particular responsibilities to the models, views, and controllers in your application. Models interact directly with the database and are responsible for the logic of your application; primarily they store and manipulate data. A common metaphor to describe the model’s job is that of a chef in a kitchen.

Views are the files (HTML, CSS, Javascript) that build the front end of your application; this is the layer that forms what the user sees and interacts with directly. The views are generally described as the “customer” in the metaphor mentioned above because they place and receive orders, which are ultimately managed by the controller and fulfilled with feedback from your models and database. Note that it is generally considered best practice to minimize the logic contained in the views.

Controllers work as the liaison between your models and your view files. They are referred to as the waiters of your application because they take requests sent from the views, communicate with the models to fulfill those requests, and take that data back to the user. 

**CRUD functionality**
CRUD refers to the basic functionality of any program that creates and persists data

**Create** —> instantiate model instances <br>
**Read** —> retrieve data from your saved model instances. This could be a particular instance or all instances of the same model. <br>
**Update** —> Edit a given model instance and persist those changes <br>
**Delete** —> Delete (also referred to as ‘destroy’! Which is much more intense) an individual instance of your model from the database

**N.B.** The language here can get a little confusing, so it is easier for me to speak in term of Ruby models and instances. However, it is worth noting that you are not saving actual Ruby instances to your database, but rather that …..(need to add to this; my apologies)


**Associations**
There is a lot that can be said of ActiveRecord associations, but ultimately they just provide functionality (i.e. methods) to your models in a way that attempts to mirror their relationships in real life. Yay for metaphors in programming! 

These relationships include (but are not limited to!) belongs_to, has_one, has_many, and has_many, :through. You can read about these and other associations [here](http://guides.rubyonrails.org/association_basics.html). 

**Validations and Other Security Features**
Still working on this; my apologies. 



**Getting Started + Tools + Getting Help**

Bearing in mind the project requirements listed above, I could basically do whatever I  wanted for my Sinatra App, provided that I didn’t do a blog post app or a Twitter clone. 

Naturally, I spent too much time fussing over what option I should choose, and I gave myself a few days to stew on what I wanted my topic to be. In typical fashion, I chose to do some research for ideas; I looked at the examples given in the project assignment, and I went to a couple of project study groups. Additionally, I creeped on the GitHub pages of every Flatiron (WeWork?) coach I could remember to see what they had done for their own projects. I would recommend that as a good place to start; it's nice to know what's in the realm of of possibility!

I still wasn’t sure what I wanted to do, though. My thought process was something like this:

**Me:** I should just do something simple, like a to-do list. People joke about this being [overdone](https://medium.freecodecamp.org/every-time-you-build-a-to-do-list-app-a-puppy-dies-505b54637a5d), but there is something to be said about not reinventing the wheel, especially as a beginner. Being basic means you learn to master the basics.&#x2028;&#x2028;

***Also me:***  But… someone will see this someday! Maybe I don’t want it to be basic. 

**Me:** Maybe I should gear my project towards a prospective employer... this could include building some type of a scheduling or organizational app; some examples we’ve seen in the curriculum have included an Amazon “cart” and an app that schedules doctor and patient appointments. 

***Also me:*** But… that’s boring! 

Ultimately, I decided that I would build something that interested me but that also targeted a group of customers/consumers and would fill a perceived need. 

That’s what apps are for, anyway, and a prospective employer should be able to appreciate that regardless of content. It's great to practice your skills, but you can do that in a myriad of ways, and someone's gotta want what you're selling. Plus, picking a topic that interests me keeps me motivated, and I won’t always have that much control over what I build.&#x2028;&#x2028;

**My app: RoutineAddict!**

**The Idea**

My app idea was inspired by the Reddit subreddit [r/SkincareAddiction](https://www.reddit.com/r/SkincareAddiction/), which has enjoyed great propularity in recent years, boasts nearly 390,000 subscribers, and usually has over 1,000 users lurking at any given time. This subreddit is a place for users to give and ask for skincare advice, talk about the science of skin and skincare, and compare products and routines. In the early stages of my app, I titled it SkynCareAddiction, but that is both obvious and ugly. Ultimately I changed the name to RoutineAddict, as it looks nicer and still gives a shout out to the original subreddit. Don't sue me!

While thinking about my app, I considered that the subreddit doesn’t have a very organized way for users to share and organize their actual routines. There are individual posts where users can share their routines, but these can get lost in other content over time and have to be searched for. There are resources and posts that talk about product recommendations (like "Holy Grail" product threads) and techniques that particular users like, but it requires some hunting around from the user if you are looking to fill a particular need or build a structured routine. I think an app that focuses on routines in particular, but also makes it easier to find routines associated with a particular user,  fills a need that is desired but not executed well in the current subreddit.

**Execution**
1. Once I had my idea fleshed out, I needed to map out my models, their attributes, and their associations.  I chose to do this on white boards (I have a bunch of big ones at home) as well as on plain old looseleaf paper. I know there are programs that let you do this on your computer, but I prefer to do it this way, for now.
2. For actually setting up my app, I used this recommended [blog post](http://blog.flatironschool.com/how-to-build-a-sinatra-web-app-in-10-steps) by another Flatiron student. Easy peasy. I am 80% sure this was mentioned by Avi in one of his lectures, but maybe I picked it up on slack?? 
3. After getting the basic structure of my project set up, adding views, controllers, models, and migrations was pretty easy. Creating seed data for my database was a little trickier (more on that later), but I figured that out as well. 
4. Next, I added most of the basic CRUD functionality, although I ran into issues with adding and updating products in a routine (more on that later). I had a couple of 1-1 meetings for help on this, as well.
5. I then made a point of going through my project multiple times, adding validations to my models and controllers and looking out for any “holes” there might be. In general, I just played around with my app a bunch, creating new users and routines and products, tweaking my views and controllers when I felt something was lacking or could be improved for a better user experience.
6. My final steps were to do some styling. To be very honest, I HATE styling and dealing with CSS. I definitely care about function more than form, but you gotta do what you gotta do. I added some very basic styling, just to bring my app one step above what you get from html right out of the box. Pretty good!&#x2028;&#x2028;
7. But then I started to wonder about adding Bootstrap…. other students had added it to their Sinatra projects… maybe I should, too? (Perhaps not the best way to make design decisions). Ultimately I ended up adding Bootstrap to my project just to prove that I could do so successfully, which I did. Soon after doing that, I decided that the bootstrap framework actually added a lot of bulk to my app, most of which I was not using and did not care to use (read: I am lazy). Next time, folks! I may go back and add more styling to my project later on, but even then I think I might keep it simple and try something like http://getskeleton.com.

