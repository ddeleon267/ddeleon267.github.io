---
layout: post
title:      "Sinatra Project Part II: Routine Addict"
date:       2017-12-20 10:58:53 -0500
permalink:  my_sinatra_app_part_ii_routine_addict
---


This post serves as a more thorough description of the main elements of my Sinatra portfolio project, namely its models, associations, controllers, and views. This app, titled Routine Addict, allows users to create and share skincare routines as well as products associated with those routines. 

 If you are unfamiliar with Sinatra or would like a more detailed introduction to my project as well as insight on my initial planning and design process, check out my last blog post. 
 
 If you'd like to read my reflections on the project as well as future considerations, peep my next blog post.

**Project Structure**

**Models and Associations**

My app has four models: users, routines, products, and routine_products; the latter specifies a join table.  

**User**

A user's atrributes include a *username*, an *email*, and a *password* (via password_digest). A user **has_many :routines**.


**Routine**

A routine has a *name* attribute, which should  be descriptive of its purpose. For example:
* Morning Routine
* Winter Evening Routine
* Simple Morning Routine for Acne-prone Skin
* Sensitive Skin Routine

A routine also has attributes of *products* (as a collection) as well as a *description*. This description could include notes about product use,  general instructions and suggestions for using particular products together, and any additional notes about skin types for whom the routine might be particularly suited (or not). A routine **has_many :routine_products** and **has_many :products, through: routine_products**.

**Products** 

A product has a *name* and a *category* (e.g. cleanser, moisturizer, sunscreen). A product also has *ingredients* and *notes*, the latter of which allows a user to add any additional information that might be important to users, like whether a product has a problematic ingredient or quality despite its benefits, or if it is particularly suited to a certain skin condition or type. 

**Routine_products + a Word on Associations**

It is worth noting that this project only required very simple model associations (at least one has_many). It is also worth noting that I ignored that advice multiple times, both in my initial planning of my project as well as when I asked around for advice on Slack.&#x2028;&#x2028;You see, I am stubborn, and I want what I want, and I was dead-set on a has_many_through relationship among some of my models. I considered the following two options:

Simpler (lol of course this is not the one I chose)
* **user has_many :routines**
* **user has_many :products, through: :routines**

* **routine belongs_to :user**
* **routine belongs_to :product**

* **product has_many :routines**
* **product has_many :users, through: :routines**
	
This option was recommended to me by an older and wiser student on Slack. It also seem to mirror the common doctor/patient/appointment relationship mentioned several times in the curriculum and also in the Active Record Association documentation. But... something about it didn’t sit right with me. In particular, it didn’t seem right for a routine to belong to a product. Avi has spoken on many occasions about using metaphors in programming to make our programs model real life, and this particular setup didn’t really do it for me. Although it made some sense to use the routine model/table as a join table, I wasn’t really crazy about that. Hence option two, which is what I went with. 

Harder (I just like to do the most)

* **user has_many :routines**

* **routine_product belongs_to :routine**
* **routine_product belongs_to :product**

* **routine belongs_to :user**
* **routine has_many :routine_products**
* **routine has_many :products, through: :routine_products**

* **product has_many :routine_products**
* **product has_many :routines, through: :routine_products**

This seemed nicer at the time, and it also felt right for a routine to not “belong” to any one user.  Although I do have some issues with that now, which I’ll mention in my next blog post.


**Migrations**

My migrations were simple enough to do once I had the associations figured out. In addition to the models and attributes mentioned above, I also made sure that my routines table had a foreign key of user_id, and that my join table **routine_products** had foreign keys of *routine_id* and *product_id*. I did end up having to do an additional migration to rename a column in my **products** table; I had initially named one the the columns “type” which is a reserved word, so I ended up renaming that to “category.”

**Controllers, Routes, and Views**	
**Application_controller**

Generally it is common to have a main application controller from which subsequent controllers will inherit. This controller contains any logic that will be used in all controllerers, like helper methods. My application controller routes to the site’s main index page (if the user is not logged in) or the user’s home page (if the user is logged in). It also contains the helper methods **#logged_in?** (returns true/false depending on whether the user is logged in) and **#current_user** (returns the current user object). 

**Users_controller**
 
**get ‘/signup’ and post ‘/signup’**
 
The ‘get’ action renders the signup form view if the user is not currently logged in, and the ‘post’ action uses that form data to either instantiate and save that user object and direct the user to her homepage (if everything looks good) or to redirect to the signup view if something went wrong. If there is an issue, a flash message is used to give the user an indication as to what it is (user already exists, invalid email, etc.)

**get ‘/login’ and post ‘/login’**

If the user is not already logged in, the ‘get’ action will render the login form view; otherwise it will redirect to the user’s home page. The ‘post’ action processes the form data and will either redirect the user to her home page or back to the login form (with a flash message) if the user data doesn’t match a current user in the database.

**get ‘/home’**

This action will direct the user to his home page, where he can easily view recently-added products, routines, and users. A user can view his home page if he is logged in, and he cannot view any other user’s home page.

**get ‘/users/slug’**

This action will route to the user’s show page, which in this instance is essentially a profile page. This page lists the routines associated with a particular user. Any logged-in user can view any user’s profile page, including that user.

**get ‘/logout’**

This action does not render a separate logout view, but instead clears the session hash to “logout” the user and then redirects to the main application index page.

**N.B.** This controller does not currently allow for a user to delete herself, in part because it did not occur to me to create this action! I think it would be worth implementing in the future, though. I do have to think about this some more, as I'm not sure what (if any) bugs this might create elsewhere in the application. 

**Routines_controller**

**CREATE**

**get ‘/routines/new’ and post ‘/routines’**

The ‘get’ action renders the form for creating a new routine for a logged-in user; otherwise it will redirect to the login page (for future reference, this will be the case for any action when a user is not logged in). The 'post' action double-checks that the user is logged in (more on that later) and uses the form data in the params hash to create a routine, associate it with the user, and save it to the database (assuming that all validations pass). If everything looks good, the controller will redirect to the show page for that routine.

**READ**
**get ‘/routines’ and get ‘/routine/:id’**

These actions render all of a user’s routines or one particular routine, respectively. It's worth noting that the content displayed in the routine index view is perhaps too similar to what is currently displayed on a user’s show/profile page, and I would tweak this in the future. 

**UPDATE**
**get ‘/routines/:id/edit' and patch ‘/routines/:id'**

The edit action renders the view for the edit form page so that  the user can edit that routine, and the patch action used the form data in the params hash to update and save the routine assuming all validations can pass. Only a logged-in user who is associated with that routine may edit that routine, and there are checks for this in both actions.

**DELETE**
**delete ‘/routines/:id/delete'**

Does what it says. If the logged-in user is associated with the chosen routine, the routine will be deleted. Otherwise the user will be redirected to the /routines page, and a flash message will confirm that the routine has been successfully deleted.

**Products Controller**

This mirrors many of the normal CRUD actions that have been outlined in the routine controller above, so I will just highlight the differences for conciseness. 

This controller has basic Create, Read, and Update functionality, with validations similar to those mentioned previously.  Two things worth noting:

1. There is no delete action for a product. There a product does not belong to any one user and can be used by many users and in many routines, so to me it did not make sense to allow a user to delete a product even if he created it. There is already a mechanism for removing a product from an individual routine, and I think that is good enough. 
2. I thought long and hard about who would be able to edit any given product. It makes sense for the user who added the product to be able to edit it, since he might make a typo or want to add something, etc. However, it also makes sense for other users to be able to edit a product for the same reasons. Keeping all of this in mind, I built the product edit and patch actions to allow any logged-in user to edit any product. 

This is not a perfect solution, and likely not the best solution for a site that actually has users! Not every user may agree on what is added to a product, although I suppose it’s a little like Wikipedia in that anyone can fix an issue. It also doesn’t prevent malicious users from creating an account and wreaking havoc on existing products, so that is a consideration. 

What might be helpful to implement in the future is to enable a commenting system (which I would like to do anyway!) so that users can suggest changes to a particular product. In that event, either the user who added the routine or site admins/mods can update the product in the database. If I can implement that type of functionality, it would make sense to allow users to only update products they themselves have added. 


