---
layout: post
title:      "Rails App : Exercise Me!"
date:       2018-04-19 12:16:34 +0000
permalink:  rails_app_exercise_me
---

For my third portfolio project (built in Rails!), I created a simple app for users to share and created workout routines and their associated exercises. You can check out my code [here](https://github.com/ddeleon267/exercise-me). 

## Models and Model Associations

This application utilizes the following models: User, Workout, Exercise, WorkoutExercise

There is a has_many/belongs_to relationship between the User and Workout models, i.e., a user has many workouts and a workout belongs to a user. 

There is a many_to_many (has many, through) relationship between Workouts and Exercises, with the WorkoutExercise model acting as the join table.

## Main Site Page 

This page includes links for a user to sign up, log in with the application, or log in with Twitter. No other options are available until the user signs up or logs in, and a logged-in user cannot view this page. 

## Traditional Signup => /signup

The user signup form has fields for the user to enter a name, email, password, and password confirmation. The name and password attribute are both mandatory, but the email field is optional (I had issues getting the third-party login to work if the email address was mandatory; as the user’s email address was not returned from Twitter). 

The User’s chosen name must be unique and at least three characters long; otherwise the signup view will be re-rendered and the user will be given a message explaining what went wrong. If the user authenticates successfully, he will be logged in (the session_id will be set to the user’s id) and redirected to his home page. 

## Login =>  /login

The login form requires the user to enter both a name and a password. A user can also log in / sign up with Twitter (discussed below). If the user authenticates successfully, the user will be directed to his home page; otherwise, the form will be re-rendered with an error message.

## 3rd Party Login with OAuth and Twitter 

I initially tried to get OmniAuth working with Facebook, which was a nightmare. I tried again with Google— no such luck. Ultimately, I was able to do it successfully with Twitter. It felt so good to finally get this working!

Adding the appropriate gems (omniauth, omniauth-twitter, dot-env) and setting up my app’s OAuth credentials was the easy part. Routing the OAuth flow was a bit more challenging!

1) There is a link in the nav bar that links to the omniauth path, in this case /auth/twitter. Upon clicking the link, the user is directed to Twitter’s authentication page and prompted to enter their Twitter credentials.

2) If the user authenticates successfully with Twitter, Twitter will redirect the user back to my application (specifically to the sessions controller #omnicreate action). If not, Twitter will re-render their login page with an error message.

3) Twitter returns a hash to my application; this contains info like the provider (twitter), as well as the user’s id (as uid), nickname, name, email, access token, etc. For whatever reason, it returns a blank email for me :( so I made the user attribute optional for signup. 

4) My application accesses this hash via the private method #auth_hash in my sessions controller; this hash will then be put to use in the sessions #omnicreate method.

5) The #omnicreate method does a few different things:

- Calls the User model class method .find_or_create_from_auth_hash, which takes in the auth_hash as an argument
- Finds a user with a uid matching that in the auth_hash, or it creates a new user in the database and sets its uid to that of the given uid. The user’s name is set to the “nickname” stored in the auth_hash.
- Generates a password for the user (using SecureRandom.hex) so that a valid password is stored in the database (Twitter is responsible for maintaining the security of the user’s actual password, but this user will need a placeholder password in order to pass validations and be saved).
- Saves the user and returns the user instance
- Logs the user in by setting the session_id to the user’s id, then redirects the user to his home page, as in any other successful user sign-in or sign-up.

## User Profile Page => /users/:id

The user show page displays the user’s name as well as any workouts that belong to the user. If the user currently logged in and viewing the page is the owner of the profile page (i.e. the session_id and user_id match), the profile will display a link to edit or delete the profile; this link redirects to the edit form view.

1) Deleting a profile: above the edit form is a link to delete the user’s account. When the user clicks this link, the request will be submitted via a delete action, first showing the user a confirmation message (“Are you sure you want to delete this?”). If the user confirms, the user instance will be deleted from the database, and the user, no longer logged in, will be redirected to the main site page. 

2) Editing a profile: The edit form will be pre-filled with the user’s name and email, but will require the user to enter a password. Assuming all validations pass when the form is submitted, the user object will be updated and the user will be redirected back to the user show page. Otherwise, the edit form view will be re-rendered and will display any errors to the user.

## Home Page (/home) and User Options

Once a user is successfully logged in and redirected to their home view, there are multiple options that are accessible via the navigation bar: adding an exercise, viewing all exercises, adding a workout, viewing all workouts, viewing a profile, and logging out. 

Additionally, the home page displays the following information to the user:

- Recently added workouts (links to the workout show page and to the profile page of the associated user)
- Recently added exercises (links to the show page for each exercise)
- Recently added users (links to the show/profile page for each user)

## Creating an Exercise => /exercises/new

The new exercise view displays a rather simple form to add an exercise; it allows the user to enter fields for an exercise’s name, primary muscle group, equipment, and a description. Only the name attribute is mandatory, and it must be 1) unique, and 2) at least 5 characters long, otherwise, the page will be re-rendered with a field describing any errors. 

The form is built using form_for (as it is directly connected to an exercise), and uses a selection dropdown to allow users to choose the muscle group attribute. 

The exercise controller #create action is pretty simple; if validations pass and the new exercise is saved, the user will be redirected to the show page for the exercise; otherwise, the form view will be re-rendered with a field for errors that describes to the user what went wrong. 

## Viewing All Exercises => /exercises 

Initially this was a pretty straightforward index action; the controller action stores all of the exercises from the Exercise class in an instance variable, which is accessed in the index view so that the name, muscle group, equipment, and description attributes can be displayed to the user. 

Because there could potentially be very many exercises, though, it made sense to allow the user to filter through exercises based on what muscle group they were looking to train, as a user might normally want to do. 

To create this functionality, I added a form (using the form_tag helper) that allows the user to select a muscle group from a dropdown menu. The form submits to /exercises via a get method. Because there is no muscle group model in my application, I had to manually enter options (as an array) for the user to select from.

If the user uses this form (i.e., the form is not left blank), the index action in the exercises controller will call the Active Record scope method #muscle_group (defined in the exercise model) on the Exercise class to store only the exercises whose muscle group attribute matches whatever the user has chosen/submitted via params. 

This method takes in an argument of muscle_group (from params) and uses a query to find all the entries with the specified muscle group:  

`scope :muscle_group, -> (muscle_group) { where("muscle_group == ?", muscle_group) }`


## Viewing an Exercise => /exercises/:id

This view is relatively simple; it displays all the attributes of the chosen exercise, along with options to:

- Edit an exercise -> This will redirect to the edit form view, with all the fields except the muscle group dropdown being pre-filled for the user. Assuming all validations pass, the exercise will be updated when the user submits the form; the user will then be redirected back to the exercise show page. If the exercise cannot be saved, the edit form view will be re-rendered and will display any errors to the user. 
- Delete an exercise  -> When the user clicks this link, the request will be submitted via a delete action, first showing the user a confirmation message (“Are you sure you want to delete this?”) and successfully deleting the exercise if the user confirms. Once the exercise is deleted the user will be redirected to the exercise index page.

## Viewing all Workouts => /workouts, /users/:id/workouts

Users can view all existing workouts via an un-nested url at /workouts, or can view all workouts associated with a user via a nested url (/users/:id/workouts). If there is an error in the nested url or the associated user is not found, the user will be redirected to his home page (maybe change this???).

If a user is specified and that user has not yet added any workouts, a message will be displayed explaining as much. Otherwise, the specified user’s workouts (or all workouts, for an un-nested url) will be displayed. This includes

- The name of the workout (links to workout show page) and the time at which the workout was last updated
- The workout description and any notes
- The name of the user associated with the workout (links to user profile/show page)

## Creating a New Workout => /workouts/new, /users/:id/workouts/new

This app allows you to create a new workout in just a few easy steps!

The WorkoutsController #new action creates an empty workout object for the workout form to wrap around.
It then creates 5 empty workout_exercise objects to store the association between each workout and exercise and to store the number of sets and reps for each workout exercise. It simultaneously builds 5 new exercise objects. It then renders the form to create the workout.

Getting the form to create a workout was definitely…. an exercise in patience.  The form seems simple enough at first glance, but A LOT OF EFFORT went into it!

The form allows the user to enter the name of the workout, along with as many as 5 names of exercises to associate with the workout (via workout_exercises). For each exercise added, the user can specify the number of sets and reps for each associated workout_exercise. Additionally, the user can add a description of the workout and any relevant notes. There is also a drop-down that allows the user to set the date the workout is added (it defaults to the current date. )

When the workout form is submitted, a few things happen

1) A Workout object is created. On this model I specified that this model accept nested attributes for workout_exercises. This allows you to create workout exercise objects from the new workout form. In rails 5, you must add the optional: true to the belongs_to associations on the join table (workout exercises) in order to instantiate the workout_exercise objects.

2) In order to handle the exercise attributes, I built an attribute setter method on my workout_exercise class that looks like this: 

`def exercise_attributes=(exercise_attributes)
    exercise_attributes.values.each do |attribute|
      if attribute != ""
        new_exercise = Exercise.find_or_create_by(name: attribute)
        self.exercise = new_exercise
      end
    end
  end`

This attribute setter, #exercise attributes, takes in a hash of attributes from and attempts to find or create an exercise object by name (if the name is not blank) using each exercise name entered in the workout form. It then creates an association between the workout_exercise and the newly found-or-created exercise. 

3) Upon form submission, any workout_exercises with blank attributes will be discarded (this is done on the workout model).

4) If the workout is successfully created (passes validations for workout name and date), the user will be redirected to the workout show page. If not, the workout form will be re-rendered (with all of the blank objects for it to wrap around), this time displaying a field for errors that explains what went wrong. 

## Viewing an individual workout => /workout/:id and /users/:user_id/workouts/:id

Users can view a specific workout via an un-nested url at /workouts/:id, or can view a workout associated with a specific user via a nested url (e.g. /users/:user_id/workouts/:id). 

- If the url is not nested, the workout is set and displayed via the id provided in the params hash. If the url is nested, the app will first attempt to find a valid user with an id matching that of the user_id provided in the params hash.

- If the user exists, the app will try to find the specified workout associated with the user and will redirect to the show page for that workout.

- If the user exists but the workout does not, the site will redirect to the workout index action nested under the user (e.g. users/:user_id/workouts), otherwise, if both the user and the associated workout are found, the site will redirect to the appropriate nested url for the workout show view (e.g. /users/:user:id/workouts/:id). Note that this will not work if the workout exists but is not associated with the specified user; the site will redirect to the nested workout index view mentioned above.  

The workout show page lists the workout’s attributes (name, description, notes, etc.). Additionally, the show page will display the name of the user associated with the workout (and link to the show page for that user, as well as the exercises associated with that workout). The name of each exercise is listed, and then the workout_exercise join model is accessed in order to display the sets and reps for each workout_exercise.

If the current user is the owner of the workout that is being viewed, a link to the the workout edit view and a link to delete the workout are displayed to the user. 

- Editing a workout -> will redirect to the edit form view, with all the fields being pre-filled for the user. Assuming all validations pass, the workout will be updated when the user submits the form and the user will be redirected back to the workout show page. Otherwise, the edit form view will be re-rendered and will display any errors to the user. 
- Deleting a workout  -> when the user clicks this link, it will be submitted via a delete action, first showing the user a confirmation message (“Are you sure you want to delete this?”) and successfully deleting the workout if the user confirms. It then redirects the user to the user_workouts_view. 


## Controller Helper Methods

There are a number of helper methods defined in the main application controller that help to dry up code elsewhere and serve to add an extra layer of security to the application.

### current_user 
Returns the current user using the user_id stored in the sessions hash
### loggedIn? 
Returns true of false depending on whether a current user exists

### require_logged_in
A user who is not logged in should not be able to see any page other than the main application page (the root page), the sign up page, or the log in page. If a user attempts to access any other page while logged in, this helper will redirect the user to the login page. 

### redirect_if_logged_in
Similarly, there are pages that a logged-in user should not be able to see, like the root page, and the login page. If a logged-in user attempts to access these pages via the url, this helper will simply redirect to user to his home page. 

### redirect_if_unauthorized
This helper is intended to protect a user’s workout from url hacking. For example, a user should not be able to create a workout under another user’s name or, more likely, attempt to edit or delete another user’s workout. 

A malicious user could try to access another user’s workout by that workout’s id (e.g., /workouts/27 or /users/27/workouts/27). In order to prevent this, this helper method checks for a few things:

- **Unauthorized user access:** accessing another user’s pages using a nested route with that  user’s user_id. In order to prevent this, the helper method will check the params harsh to see if a :user_id parameter exists (as it would if the url were nested). If the user_id does exist, the helper will check to see if this :user_id matches the id of the currently logged-in user. If it does not, this statement evaluates to true.

- **Unauthorized workout access:** accessing a workout that belongs to another user.. This can easily be done whether or not the route is nested. To prevent this, the helper will access the :id parameter in the params hash (which specifies a workout) and check the currently-logged in user’s workouts to see if the user’s workouts include this particular workout. If that is not the case, this statement evaluates to true.

If either of these two conditions evaluates to true, the user will be not be granted access to the requested page but will instead be re-directed to his own home page.


And that's my app! Check out my code!
