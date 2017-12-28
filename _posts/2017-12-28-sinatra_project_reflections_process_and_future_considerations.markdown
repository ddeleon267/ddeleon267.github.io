---
layout: post
title:      "Sinatra Project: Reflections, Process, and Future Considerations"
date:       2017-12-28 20:57:06 +0000
permalink:  sinatra_project_reflections_process_and_future_considerations
---


This post is intended to be a general reflection on the process of building my Sinatra Portfolio Project and blogging about it. If you are interested in my [code](https://github.com/ddeleon267/routine-addict) or in a more technical description of my project, I would direct you toward [my last blog post](http://ddeleon.com/my_sinatra_app_part_ii_routine_addict), which will (eventually) include a link to my video walkthrough of the app.

This post includes some navel-gazing about my journey through coding and blogging, mingled with some more straight-forward reflections on my project and process. If you are more interested in the latter, feel free to skip past this next section. 


*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *
**A Word on Blogging**
<br>


In my last Final Project blog series, I conceded that it's a bit excessive to have three blog posts on a single project. While I suppose that's still true, I'm okay with it. And I'll keep doing it. Although...

<br>

...in a way, doing these blog posts has always been a pain in the ass. 

<br>

Writing can be exhilarating if you're into what you're writing about and you get a good flow state going. Basically every other time, though, it's terrible. It's difficult, and it's personal, and it's messy.  

If it isn't, your writing probably isn't very good. (That's what I tell myself, at least.)


It's easy to dismiss blogging as stupid. Which I usually do! 


* "Not every thought deserves a platform."
* "The Internet is littered with blogs at this point."
* "I'd rather just wait until I have something meaningful to say."

<br>


These are the things I tell myself when I should be blogging. And while I still say that at least the first two are true (if cynical), the last is mostly bull. 

No one is looking to my blog for award-winning content. ***It's not that serious.*** My blog is a place for me to practice putting coding concepts into words so that I can 1) become better at articulating those ideas and 2) better understand the content I'm writing about. 

Ideally, people will read my blog and learn from it, or perhaps agree that I know a few things about what I'm writing about.  Neither of those happen unless I work on the first two things, though.

**Writing helps you learn.** I know that this is objectively true, and I repeated the sentiment many times to my students amidst cries of "But it's math class!" and "But why are we writing in physics?" And yet, I am stubborn and lazy and fearful when it comes to doing this for myself. Why am I like this? I'm afraid to put myself out there.

And so, here on this blog I try to suck it up and face the music. Plus, if most content on the Internet is mediocre, that should suggest the barrier to entry is rather low, which would mean that I have no excuses for not writing my blog posts. 

So, with that said, I will try to embrace blogging in the future, at least as far as my projects go. 

![](http://media4.picsearch.com/is?xUGXjXg8tvWdLhft37We_XauKZcLHp8B0c8kKe8ak8U&height=255)

Without further ado, here are my project-related thoughts.

<br>
*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *

<br>
(For context: my Sinatra App, RoutineAddict, allows users to create and share skincare routines and related products. It was inspired by the Reddit subreddit r/SkincareAddiction.)

**A Brief Description of My process**

To be honest, for me this project was MUCH easier than my CLI Gem Project.  A lot of that difference was just getting over some of the mental hurdles that make it hard to get started, but this project also just felt... simpler. My general process:

1. Once I had my idea fleshed out, I needed to map out my models, their attributes, and their associations.  I chose to do this on white boards (I have a bunch of big ones at home) as well as on plain old looseleaf paper. I know there are programs that let you do this on your computer, but I prefer to do it this way for now. I initially was over-enthusiastic with my model attributes, then pared things down so I wouldn't have a herculean task on my hands as I was setting up my forms and controllers. I kind of regret that, though, as I had difficulty adding attributes later (my database wouldn't update) and elected to just save that for another time. 

2. For actually setting up my app, I used this recommended [blog post](http://blog.flatironschool.com/how-to-build-a-sinatra-web-app-in-10-steps) by another Flatiron student. Easy peasy. I am 80% sure this was mentioned by Avi in one of his lectures, but I might have picked it up from someone on Slack.
 
3. After getting the basic structure of my project set up, adding views, controllers, models, and migrations was pretty easy. Creating seed data for my database was a little trickier (more on that later), but I figured that out as well. 

4. Next, I added most of the basic CRUD functionality, although I ran into issues with adding and updating products in a routine (more on that later). I had a couple of 1-1 meetings for help on this, as well.

5. I then made a point of going through my project multiple times, adding validations to my models and controllers and looking out for any “holes” there might be. In general, I just played around with my app a bunch, creating new users and routines and products, tweaking my views and controllers when I felt something was lacking or could be improved for a better user experience.

6. My final steps were to do some styling. To be very honest, I HATE styling and dealing with CSS. I definitely care about function more than form, but you gotta do what you gotta do. I added some very basic styling, just to bring my app one step above what you get from the browser right out of the box. Pretty good!

7. But then I started to wonder about adding Bootstrap…. other students had added it to their Sinatra projects… maybe I should, too? (Perhaps not the best way to make design decisions). Ultimately I ended up adding Bootstrap to my project just to prove that I could do so successfully, which I did. Soon after doing that, I decided that the bootstrap framework actually added a lot of bulk to my app, most of which I was not using and did not care to use (read: I am lazy). Next time, folks! I may go back and add more styling to my project later on, but even then I think I might keep it simple and try something like http://getskeleton.com.

8. More poking around and tweaking and debugging, and... here we are!

<br>

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *

<br>

**What I Have Learned**

***The most significant thing that I have learned while working on this project is to keep things simple.***

When you do things the hard way, don't be surprised when you struggle.

When I look back at my last blog host for my CLI Gem Project, there are a lot of concrete things that I learned. There ***are*** some things I learned to do while working on my Sinatra project; certainly there are plenty of things I learned in the curriculum that I needed in order to successfully build this app in the first place! 

That being said, the biggest thing I learned while working on this project is to **not overcomplicate things**.

Once I have an idea, I roll with it and I want things to be done a certain way. But, while it's well and good to be aspirational, it's easy to become arrogant and assume that you know the best way to do things -- despite well-intentioned advice from others. If you've read my previous blog posts on this project, you'll know that I'm taking about the model associations for my project! I won't re-hash it all here, but in a nutshell: I went with multiple **has_many, :through** associations (with a join table and all!) in my project. 

Even though I probably could have gotten away with much simpler associations to achieve most of the same functionality, and even though the project itself only required very simple model associations, I wanted to do what I wanted to do. It wasn't the *worst* idea ever in terms of the functionality I had in mind. It was an informed decision, I looked at similar examples to get a feel for how I would start, and I felt it was the best way to handle my associations. I asked for help on Slack anyway and was advised by another student to do something simpler, but I still chose to chug ahead with my original idea.

As a result, I really had a painful experience creating the **'post'** and **'patch'** actions in my **Routines_Controller**. The **:products** attribute wasn't behaving correctly (a routine **has_many :products, through: :routine_products**) whenever a routine was created or edited. I really struggled to get that collection to behave appropriately, and I had to have one or two 1-on-1s to fix the issues I was having.


There's plenty of attention paid to **has_many_through** relationships in the Rails curriculum, and for good reason: it can get messy! I had to learn this the hard way (and sooner than I needed to) because I didn't want to keep things simple. On the bright side, my spidey senses are telling me that my Rails project will probably require a has_many_through  association, so.... at least I got some practice???

<br>

---
<br>

In a previous blog post I talked about some of the content knowledge I needed to build this project successfully (Sinatra, controllers, routes, views, etc), so I won't touch on any of that content here. But there are a few other concrete skills that I had to  learn as I grappled with my project:

I learned how to:

* Successfully create a Sinatra App that 1) connects to a database 2) has appropriate gem dependencies 3) has the correct file structure
* Become more comfortable with methods added by Active Record associations
* Create working forms! With check boxes!
* Successfully implement flash messages
* Use some common ActiveRecord validations
* Implement very basic security features to protect my (hypothetical) users
* Properly seed a database (this was actually rather difficult! I looked at examples for similar projects and played around till things worked correctly.)
* Drop/reset my database (and restart the auto-incrementing!) when I overrode my database in a horrendous fashion
* Create a new git repo without losing my previous progress and commits (because I screwed up the file/folder structure and didn't realize till I was 80% done!)
* Improve my technical writing so that it is less of a snoozefest to read          
* Evaluate my code for DRYness and readability

A lot of these lessons were learned through good old-fashioned struggle, but I learned them nonetheless!
<br>

----

<br>
**Project: Future Considerations**

I am pretty happy with how my app turned out, but there are a number of things that I would love to tweak in the future: 

* **Formatting and styling:**. Currently I have a minimum amount of styling, as it isn't really the focus of these first couple of projects. At a minimum I'd like to at least tweak the styling of my "new" and "edit" forms so they look a little less clunky and more elegant. 
* **Attributes:** In general, I would like my models to have more attributes in order to allow users to tailor their experience based on these attributes. For users, attributes of  **skin_type**, **skin_concerns**, and **climate** would be helpful and would allow users to find others with similar attributes in order to explore their routines. Additionally, I would add an attribute to keep track of when a user is added, as that adds some "weight" to a user's routine and product recommendations. It would be nice to add "user since X" to a user's profile page! In the same vein, I want to add other attributes to the routine and product models.
* **Option to edit/delete user:** Currently there are no forms, routes, or actions that allow a user to edit his own attributes (i.e. edit your profile page!) or even delete her own account. These are pretty standard features in other applications with user acounts, so I want to implement this functionality a well. 
* **Editing products:** As the project currently stands, any logged-in user can edit any product. This is not great, as not every user might agree to a change made to a product, or someone might make changes with bad intentions. I thought of making it so that only a user who added a product can edit it, but I was not sure how to implement that (since a product does not **belong_to** a user). That also would not be a perfect solution; if another user notices errors in a product that was not added by him, what can be done about it? I'm not sure what the best fix is, but since my app currently doesn't have any real users, I'm content with the current situation. 
* **Security features:** There are a few things that would be nice to implement for security. For example, on an episode of Front End Happy Hour, the hosts talked about security vs. user experience in regard to login attempts. For a good user experience, it is nice to implement a feature (like flash messages in my project) to tell a user why the login attempt failed. But, for someone trying to get into your account by brute force (i.e. trying again and again) this makes their job easier by giving the hacker feedback! For this reason, it would be nice to minimize the amount of times a user can log in incorrectly, like locking your account after 3 incorrect tries. It's not a perfect solution, but something like this in addition to other features (like emailing the user to alert them to change their password, password retrieval options, etc.) would be nice.
* **Commenting system:** Thinking further ahead, it would also be nice to implement other features like a commenting system where users can offer feedback, questions, and just general discussion about individual routines and products. It would also be nice for users to be able to follow each other and get notifications... 
* ...I could go on and on! But I can't stay on this one project forever!
<br>

----
<br>

**Postscript: Get On With It!**

I feel pretty good about my project, and I'm ready to move on. Like my next project, it will never be "finished," but I think that is the nature of the beast. There's an up and a down to creating things, whether it is an app, a blog post, or something else. Apprehension, excitement, struggle, progress, doubts, success!, revision, and ... closure. Not necessarily in that order, though, and I'd wager you hit most of those stages multiple times.

The ride gets easier as you practice, which I'm trying to remind myself of as I work through the struggle-y bits. One thing that I'm becoming more cognizant of, though, is that a lot of the **ANXIETY** that comes with this process is self-imposed. 

I touched on this in the beginning of this post -- being to afraid to write blog posts (masking my fear as being "above" it all), for example. I talked about procrastination and imposter syndrome in another blog post, as well. 

***Don't let your insecurities get in the way of your progress.*** -- Avi

While all of these feelings -- the ones that keep you from getting things done because you are afraid of being judged, or exposed, or just getting stuck again -- are valid to some extent. Mostly, though, they are in YOUR head.  To take an extreme example:

I delayed asking for help on my project MULTIPLE times because my topic (skincare) skews towards the ladies. Even though I know plenty of men are interested in skincare (and the Reddit subreddit my project was inspired by has a number of prominent male users!) I was afraid of asking for help because I didn't want to be judged for my "girly" topic. My genuine interest in my topic mattered less to me than how it would be perceived by others. When I went on Slack for help, I went straight to the @women_in_tech channel for precisely this reason.

Sure, there is some validity to these feelings. Most coders are men, whether we are talking in general terms or specifically about Learn-ers on Slack or about technical coaches. Men are perhaps less interested in skincare overall (I don't know the numbers here, honestly), so that might make it the *slightest* bit harder for a man to initially "get" my project when I'm asking for help on something. But... probably not.

And honestly? Most people aren't assholes, despite what any comments section would lead you to believe. While it is true that women in tech are often casually dismissed for being interested in stereotipically "feminine"  things, most people aren't going to openly ridicule you because you're asking for help on something they aren't actively interested in. If they do, they suck, and there are plenty of other people to seek help from. Next!

So, sometimes you have to get over yourself. Be afraid, be anxious, be whatever it is that you are, but get things done. 

Feelings don't matter. Work harder!





