---
layout: post
title:      "Sinatra Project Part I: What is a Sinatra App? + Getting Started"
date:       2017-12-18 14:32:04 -0500
permalink:  sinatra_project_part_1_what_is_a_sinatra_app_getting_started
---

(Note that this is a VERY rough draft and only serves as an initial blog post for my Sinatra Project. In particular, it is a brief intro to Sinatra + a VERY basic overview of the design stages for my project. There will be subsequent posts that 1) go through my app in more detail and 2) reflect on my project and process as well as explore difficulties and future considerations. Links to my project and video walkthrough will also be added later)

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *          
**Intro**

This is intended to be a brief introduction to my Sinatra App with a little background for those who are unfamilliar with Sinatra applications

This is my second major portfolio project, and the requirements are pretty simple. The app must:

* Be built in Sinatra using MVC conventions and full CRUD functionality
* Have multiple models and > 1 has_many relationship
* Have user accounts with validations for signup
* Include safety features, such as validations, that keep users who aren’t you from editing your content!

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         * 

![](http://budiirawan.com/wp-content/uploads/2015/06/sinatra-logo.png)

**Sinatra**

[Sinatra is a lightweight, Rack-based Ruby framework](https://learn.co/tracks/full-stack-web-development-v3/sinatra/sinatra-basics/what-is-sinatra) that is used to make dynamic web applications. When I say lightweight, that's compared to the big guns you get with Ruby on Rails. Rails is powerful and gives you everything you need to build an application "from front to back." That being said, it adds a lot of bulk and can be hard to debug if you're not sure what you're dealing with. Sinatra gives you the bare minimum, so while you won't necessarily have your app up and running right away, you have fewer moving parts to worry about if something goes wrong. You also have to set up much of your app manually, which is great practice for a beginner!

 The Sinatra community seems fun and sassy as well, and the [documentation](http://sinatrarb.com/documentation.html) is easy to read and digest (compared to, ahem, Rails documentation). 

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         * 

**MVC**

MVC, or [Model-View-Controller](https://learn.co/tracks/full-stack-web-development-v3/sinatra/mvc-and-forms/intro-to-mvc), is a programming design paradigm/pattern that designates particular responsibilities to the models, views, and controllers in your application. *Models* interact directly with the database and are responsible for the logic of your application; primarily they store and manipulate data. A common metaphor to describe the model’s job is that of a chef in a kitchen.

*Views* are the files (HTML, CSS, Javascript) that build the front end of your application; this is the layer that forms what the user sees and interacts with directly. The views are generally described as the “customer” in the metaphor mentioned above because they place and receive orders, which are ultimately managed by the controller and fulfilled with feedback from your models and database. Note that it is generally considered best practice to minimize the logic contained in the views.

*Controllers* work as the liaison between your models and your view files. In the restaurant metaphor they are referred to as the waiters of your application because they take requests sent from the views, communicate with the models to fulfill those requests, and take that data back to the user. 

![](http://www.how-to-draw-funny-cartoons.com/image-files/cartoon-waiter-008.jpg)


*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         * 

**CRUD functionality**

[CRUD](https://learn.co/tracks/full-stack-web-development-v3/sinatra/activerecord/activerecord-in-sinatra) refers to the basic functionality of any program that creates and persists data

**Create** —> instantiate model instances <br>
**Read** —> Retrieve data from your saved model instances. This could be a particular instance or all instances of the same model. <br>
**Update** —> Edit a given model instance and persist those changes <br>
**Delete** —> Delete (also referred to as ‘destroy.’ Which is much more intense!) an individual instance of your model from the database

**N.B.** The language here can get a little confusing, so it is easier for me to speak in term of Ruby models and instances. However, it is worth noting that you are not saving actual Ruby instances to your database, but rather that you have [*wrapped* your database with an ORM like ActiveRecord](https://learn.co/tracks/full-stack-web-development-v3/orms-and-activerecord/orms/why-an-orm-is-useful) such that your Ruby models *correspond* to tables in your database and instances of those models *correspond* to individual entries (i.e. rows) in that database. By doing this, you can happily code in Ruby while ActiveRecord handles the SQL required to modify and retrieve data from your database. 

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         * 

**Associations**

There is a lot that can be said of [ActiveRecord associations](https://learn.co/tracks/full-stack-web-development-v3/orms-and-activerecord/associations/activerecord-associations), but ultimately they just provide functionality (i.e. methods) to your models in a way that attempts to mirror their relationships in real life. Yay for metaphors in programming! 

These relationships include (but are not limited to!) belongs_to, has_one, has_many, and has_many, :through. You can read about these and other associations [here](http://guides.rubyonrails.org/association_basics.html). 

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         * 

**Validations and Other Security Features**

Website security is a BIG topic and my knowledge of it (so far!) is very limited, but I'll just touch on a few things that are expected to be included in this project. The first is not a "security" feature per se but is related to protecting the integrity of your database: validations. The second is more of what you'd expect; protecting sensitive information from your users and making sure they can only view content they're supposed to!

Validations are a means of protecting your database from "bad" data: data that shouldn't even be saved to your database in the first place. [Active Record validations](https://learn.co/tracks/full-stack-web-development-v3/rails/validations-and-forms/activerecord-validations) are method calls that you add to your models that prevent instances of the class from being saved if something is off, like if an email is in the wrong format, a chosen username is already taken, or if required data is missing when a form is submitted. Ideally, if you're using ActiveRecord validations you should also provide a means (like a flash message) for letting the user know something has gone wrong so that the problem can be correctly. You can get MUCH more information about Active Record Validations [here](http://guides.rubyonrails.org/active_record_validations.html).

Some security is handled in your controllers as well; these include basic tasks like only allowing logged-in users to view certain pages of your application and routing appropriately if the user is not logged in, is not in the database (so is not a current user or has entered bas username/password information), or has otherwise submitted bad form data. Helper methods like **#logged_in?** and **#current_user** are helpful here, as you can add conditions to your controller actions and reroute appropriately depending on whether thoose conditions are met. This will help keep a user's personal pages hidded from other users. It's worth noting that you should consider security on both "sides" of any form action, i.e. both the get/put actions as well as any post, patch, and delete actions so that no one is able to "html hack" your form data!

Last but not least (seriously, it's the MOST important thing): password. It is on you to protect the sensitive data that your users entrust to you, especially as far as passwords are concerned. Not everyone is great about using strong and unique passwords, so there are a few added layers of security that you need should the worst happen and a malicious actor gets into your database. Again, this is a big topic, but some simple things you can (and should!) include at this level are [still working on this; apologies!]

