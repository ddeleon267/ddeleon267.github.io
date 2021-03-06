---
layout: post
title:      "Sinatra Project: Routine Addict!"
date:       2017-12-20 10:58:53 -0500
permalink:  my_sinatra_app_part_ii_routine_addict
---


This post serves as an outline of my Sinatra portfolio project, including its models, associations, controllers, and views. The app, titled Routine Addict, allows users to create and share skincare routines as well as products associated with those routines. 

You can find a video walkthrough of my app [here](https://www.youtube.com/watch?v=Fo2jpYNWKR4) or visit the git repo [here](https://github.com/ddeleon267/routine-addict).

 If you are unfamiliar with Sinatra, peep [my last blog post](http://ddeleon.com/sinatra_project_part_1_what_is_a_sinatra_app_getting_started). If you want to learn more about my planning process for this project or my current reflections on it, check out [this blog post](http://ddeleon.com/sinatra_project_reflections_process_and_future_considerations). 
 
 
*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *

**Project Structure**

***Models and Associations***

My app has four models: users, routines, products, and routine_products; the latter specifies a join table.  

**User**

A user's atrributes include a *username*, an *email*, and a *password* (via password_digest). A user **has_many :routines**.

This class contains validations for the presence of the username, email, and password attributes; it also validates for username uniqueness, email formatting, and password length.

The user class also contains two helper methods. The first method, #slug,  returns a ["slugged"](https://en.wikipedia.org/wiki/Semantic_URL#Slug) verson of a user's username. The second is a class method that finds and returns a matching user object (by slug) if it exists. 


**Routine**

A routine has a *name* attribute, which should  be descriptive of its purpose. For example:
* Morning Routine
* Winter Evening Routine
* Simple Morning Routine for Acne-prone Skin
* Sensitive Skin Routine

A routine also has attributes of *products* (as a collection) as well as a *description*. This description could include notes about product use,  general instructions and suggestions for using particular products together, and any additional notes about skin types for whom the routine might be particularly suited (or not). A routine **has_many :routine_products** and **has_many :products, through: routine_products**.

This class validates the presence of the routine name, products, and description attributes.

**Products** 

A product has a *name* and a *category* (e.g. cleanser, moisturizer, sunscreen). A product also has *ingredients* and *notes*, the latter of which allows a user to add any additional information that might be important to users, like whether a product has a problematic ingredient or quality despite its benefits, or if it is particularly suited to a certain skin condition or type. 

This class validates the presence of the product name.

**Routine_products + a Word on Associations**

It is worth noting that this project only required very simple model associations (at least one has_many). It is also worth noting that I ignored that advice multiple times, both in my initial planning of my project as well as when I asked around for advice on Slack. You see, I am stubborn, and I want what I want, and I was dead-set on a has_many_through relationship among some of my models. I considered the following two options:

Simpler (lol of course this is not the one I chose)
* **user has_many :routines**
* **user has_many :products, through: :routines**

* **routine belongs_to :user**
* **routine belongs_to :product**

* **product has_many :routines**
* **product has_many :users, through: :routines**
	
This option was recommended to me by an older and wiser student on Slack. It also seemed to mirror the common doctor/patient/appointment relationship mentioned several times in the curriculum and also in the Active Record Association documentation. But... something about it didn’t sit right with me. In particular, it didn’t seem right for a routine to belong to a product. Avi has spoken on many occasions about using metaphors in programming to make our programs model real life, and this particular setup didn’t really do it for me. Although it made some sense to use the routine model/table as a join table, I wasn’t really crazy about that. Hence option two, which is what I went with. 

Harder

* **user has_many :routines**

* **routine_product belongs_to :routine**
* **routine_product belongs_to :product**

* **routine belongs_to :user**
* **routine has_many :routine_products**
* **routine has_many :products, through: :routine_products**

* **product has_many :routine_products**
* **product has_many :routines, through: :routine_products**

This seemed nicer at the time, and it also felt right for a routine to not “belong” to any one user.  (Although I do have some issues with that now, which I’ll mention in my next blog post.)

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *

***Migrations***

My migrations were simple enough to do once I had the associations figured out. In addition to the models and attributes mentioned above, I also made sure that my routines table had a foreign key of *user_id*, and that my join table **routine_products** had foreign keys of *routine_id* and *product_id*. I did end up having to do an additional migration to rename a column in my **products** table; I had initially named one the the columns “type” which is a reserved word, so I ended up renaming that to “category.”

*         *          *          *          *          *          *           *          *          *         *          *          *          *          *          *         *          *          *          *         *

***Controllers, Routes, and Views***	

**Application_controller**

It is common to have a main application controller from which subsequent controllers will inherit. This controller contains any logic that will be used in all controllers, like helper methods. My application controller routes to the site’s main index page (if the user is not logged in) or the user’s home page (if the user is logged in). It also contains the helper methods **#logged_in?** (returns true/false depending on whether the user is logged in) and **#current_user** (returns the current user object) to help streamline some of the conditional statements that will be needed in other controllers. 

**Users_controller**
 
*get ‘/signup’ and post ‘/signup’*
 
The ‘get’ action renders the signup form view if the user is not currently logged in. The ‘post’ action uses that form data to  instantiate and save a new user object if all validations pass; in that case, the controller will redirect to the user's homepage. Otherwise, the controller will redirect to the signup view, which will render a flash message to give the user an indication as to what the problem was (user already exists, invalid email, etc.)

*get ‘/login’ and post ‘/login’*

If the user is not already logged in, the ‘get’ action will render the login form view; otherwise it will redirect to the user’s home page (for future reference, this will be the case for any action when a user is not logged in). The ‘post’ action processes the form data and will either redirect the user to her home page or back to the login form (with a flash message explaining the error) if the user data doesn’t match a current user in the database.

*get ‘/home’*

This action will direct the user to his home page, where he can easily view recently-added products, routines, and users. A user can view his home page if he is logged in, and he cannot view any other user’s home page.

*get ‘/users/slug’*

This action will route to the user’s show page, which in this app is essentially a profile page; this page lists the routines associated with a particular user. Any logged-in user can view any user’s profile page, including that user.

*get ‘/logout’*

This action does not render a separate logout view, but instead clears the session hash to “log out” the user and then redirects to the main application index page.


**Routines_controller**

**CREATE**

*get ‘/routines/new’ and post ‘/routines’*

The ‘get’ action renders the form for creating a new routine for a logged-in user. The 'post' action double-checks that the user is logged in and uses the form data in the params hash to create a routine, associate it with the user, and save it to the database (assuming that all validations pass). If everything looks good, the controller will redirect to the show page for that routine. Otherwise, the controller will redirect to the 'get' action and the view will render a flash message that explains what went wrong.

**READ**
*get ‘/routines’ and get ‘/routine/:id’*

These actions render the views for all of a user’s routines or for one particular routine, respectively. 

**UPDATE**
*get ‘/routines/:id/edit' and patch ‘/routines/:id'*

The edit action renders the edit form view,  and the patch action uses the form data in the params hash to update and save the specified routine if all validations pass. 

Only a logged-in user who is associated with the specified routine may edit that routine, and there are checks for this in both update actions.

**DELETE**
*delete ‘/routines/:id/delete'*

Does what it says! If the logged-in user is associated with the chosen routine, the routine will be deleted. Otherwise the user will be redirected to the /routines page, and a flash message will confirm that the routine has been successfully deleted.

Note that this action does not have its own view but is accessed via a button on the routine show page.

**Products Controller**

The actions in this controller generally mirror the normal CRUD actions that have been outlined in the routine controller above, so I will just highlight the differences for conciseness. 

This controller has basic Create, Read, and Update functionality, with validations similar to those mentioned previously.  Two things worth noting:

* There is no delete action for a product.  A product does not belong to any one user and can be used by many users and in many routines, so to me it did not make sense to allow a user to delete a product even if he created it. There is already a mechanism for removing a product from an individual routine, and I think that is sufficient. 
* I thought long and hard about who would be able to edit any given product. It makes sense for the user who added the product to be able to edit it, since he might make a typo or want to add something, etc. However, it also makes sense for other users to be able to edit a product for the same reasons. Keeping all of this in mind, I built the product 'edit' and 'patch' actions to allow any logged-in user to edit any product. 

This is not a perfect solution, and likely not the best solution for a site that actually has users! Not every user may agree on what is added to a product, although I suppose it’s a little like Wikipedia in that anyone can fix an issue. It also doesn’t prevent malicious users from creating an account and wreaking havoc on existing products, so that is a consideration. 

What might be helpful to implement in the future is to enable a commenting system (which I would like to do anyway!) so that users can suggest changes to a particular product. In that event, either the user who added the routine or site admins/mods can update the product in the database. If I can implement that type of functionality, it would make sense to allow users to only update products they themselves have added. 

That's my app! Check out my code!


