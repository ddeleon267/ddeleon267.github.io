---
layout: post
title:      "CLI Gem Project: Final Thoughts"
date:       2017-10-19 14:21:03 -0400
permalink:  cli_gem_project_final_thoughts
---


It's a bit excessive to have three blog posts on the same project, but I have a number of thoughts on the matter, and they are scattered all over. With anything that you create, there are a bunch of moving parts -- not just in the literal sense, but also in the ways that you think about what you are doing, what you plan to do, and what you have already done. It didn't really make sense to me to put it all in one place.

It contains multitides.

If you are looking for more of a technical description of what my project does, I would encourage you to visit [my last blog post](http://ddeleon.com/cli_gem_project_whats_on_tap), where you'll also find links to the git repo for the gem and a brief video that gives a demo from the user's perspective. If you're more interested in my initial thoughts/reflection on the project, the beginning stages of planning, etc., I would direct you to the [post](http://ddeleon.com/cli_gem_project_initial_thoughts) I wrote immediately before that one. 

This post will likely be similar in tone to the latter, which is how I prefer to write. 

 **Process: What I Have Learned**

**The most significant skill that I have taken away from this endeavor is learning how to debug with pry.** 

While going through the Learn / Flatiron curriculum, you're introduced to a number of tools early on to make sense of what your program is doing and what is breaking; this includes error messages, spec tests and their results, and pry. For one reason or another, I just didn't feel comfortable with pry right off the bat, wasn't sure how to make use of it, and so I over-relied on the first two tools when going through labs. More often than not I could get away without using pry at all, and I only really made use of it when forced to do it on a screen share with a tech coach. 

When you're going through a project of your own making, though, you don't really have that luxury. I could have written spec tests for my project, and I considered doing so, but eventually I had to force myself to dig into my code and see what was what. **You can't shape your code if you don't even know what its various parts are doing!** While I certainly can't claim to be an expert on Pry or on debugging in general, I can say that I am infinitely more comfortable with my code and my ability to poke around and observe its behavior.

I also learned a ton about making forward progress in the midst of uncertainty -- and in the midst of decision paralysis. I have a tendency to over-research before taking concrete action on a task, as I think I have mentioned before. While it's nice to know things and you have to do your due diligence before asking for help, it can also easily become a time-suck and a way of hiding from what you don't know how to do. 

These are some of the methods that I used to push myself foward when I was uninspired, stuck, or not sure why my code wasn't behaving how I wanted:

* Following examples from video lectures and project samples

* "Do what works and fix the rest later" i.e. following Avi's examples to start out with stubbed data and simple solutions so that you build momentum and tweak things later

* Modeling (whiteboarding, pen and paper, stubbed data, thinking about the code that "I wish I had") to visualize what I wanted my program to do and how I wanted it to behave. Part of this was thinking in terms of models / objects / metaphors, some of it was just imagining what I wanted things to look like from the user's perspective, and a lot of it was just explicitly naming what each part of the proces would involve. 

* Debugging (as I mentioned already) 

* Asking for help. A lot of things can be handled with a trip (or five) to google or to Stack Overflow. But, when I was really stuck in the beginning of my project, as well as approaching the refactoring/"end" stages, I had to suck it up and reach out to a few technical coaches. One DM conversation and three one-on-ones later, my code is much better -- and I have received at least a few affirmations that my ideas are not totally bananas (and I might actually have some good ones). 


On a technical note, I have also learned a few things in the process of creathing this gem, including how to:

* create a gem with an appropriate file structure, with working file and gem dependencies
* explain my code to others and write technically about what my code is doing-- this was MUCH harder than I anticipated
* refactor my code without explicit instructions on how to do do
* evaluate naming conventions, descriptiveness, and how my code is organized
* url encoding
* modify urls, particularly using search queries
* scrape (somewhat) effectively.

I built a gem, but I built some concrete skills as well :)

** Project: Future Considerations**

While I am proud of the gem I have created and I stand by my work, there are a few things that I wish had turned out differently and would love to come back and tweak in the future. I will focus on main concerns rather than little nitpicky things:

* My initial goal was to create a gem that takes in a user's zip code (not city) and displays results based on which locations are *closest*, giving data about how far each location is from the user. For the sake of making the completion of this project manageable and not totally overwhelming, I was encouraged to use the city input instead. As a result, the current gem is missing that additional data (how far away the location is) and these is no obvious hierarchy to the results that are listed. As a user, it is infinitely less useful for me to have a list of any 5 locations that happen to be in my city.

* My reason for calling the gem WhatsOnTap was that I specifically wanted access to "sit-down" places where users could find craft beers *on tap*. I'm not really sure how to articulate why this is so important to me, but I wanted this gem to help users create experiences -- not just places to *get* beer but places to sit down, explore, and enjoy beer. In practical terms, this would mean being much more selective about what data is being scraped for -- and in turn, which locations are being instantiated / saved / presented to the user. I would rather my gem omit locations that only sell bottles, are "to go" places, have no or few "craft" beers at all, etc. That's just more in line with what my initial vision was, even if it means presenting fewer options to the user (although, if I were being more particular about which results are displayed, I would be more than happy to give users more than 5 results for each query).

* My current code is very reactive (particularly in the scraper class) with regard to keeping things from breaking. Because my code has to work for so many different situations (you can literally pick any city!), and not all data is provided for all results, there are things that I had to go back and fix after extensve testing. This leads to some clunky conditional statements, case statements, etc. My code doesn't look elegant or intentional in some places. I would like to develop a better knack for anticipating where things might go wrong, rather than just making small fixes here and there when things do. On that note, while I have done a great deal of testing and currently have not found things breaking, reality tells me that there are still places where this code will ultimately break.

* My code has some "back up plans" to avoid breaking things whenever possible when things do not go as planned (e.g. telling the user there is no data to display). While the majority of my testing / debugging shows that things DO go as planned, I am seeing these "back ups" way more often than I should be, which tells me that I need to be more particular in how I am scraping. It's not a deal-breaker -- but I am not totally satsfied, either. 

**Postscript: Onward and Upward**


Once I really gained momentum on the project, things begain to feel good.  The initial steps -- building a file structure with the correct dependencies, scraping successfully, instantiating real objects from what has been scraped -- were daunting and uncertain. I questioned my life choices more than I'd care to admit. 

When you're in that space, it's very easy to question what you're doing, which unfortunatly often leads you to question whether you're even qualified or smart enough to make these types of decisions at all. Most people experience this to a degree -- they don't call it "imposter syndrome" for nothing-- and I know that it's something I'll struggle with and continue to work through as time goes on. I am particularly sensitive to this -- as a woman, as a POC, and as someone who didn't really grow up around "nerd culture" or people encouraging me to understand and be interested in technology -- and that is a sore spot that is on me to handle and overcome. 

Once I gained some traction on my project, though, I got excited about what I was doing. Excited enough to have opinions that were more nuanced and useful than "this is terrible and will look stupid." Truth be told, all of a sudden I was in no rush to be done, and I was going through my code again and again, tweaking things  to fit my own preferenes (I'm starting to have preferences!) rather than just putting out fires. 

**I have now reached the point where it is done.** It is not perfect, but it is good, and it achieves its objectives well. I once had a mentor who used to say that "better than great is lousy." I think that's what people mean when they say that 
"perfect is the enemy of good."

While ultimately I believe I will come back and make my gem better using what I have learned by then, I also recognize that my enthusiasm for it has waned, and it is time move on.

For now.




