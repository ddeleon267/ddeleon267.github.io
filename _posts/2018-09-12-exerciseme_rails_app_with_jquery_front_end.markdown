---
layout: post
title:      "ExerciseMe : Rails App with JQuery Front End"
date:       2018-09-12 10:09:19 +0000
permalink:  exerciseme_rails_app_with_jquery_front_end
---


For this project, I built on the Rails App I described in my last blog post; I just had to implement jQuery to do the following:

	-render and index page/resource
	-render a show page/resource
	-create and render a resource to the page without a page refresh

Plus a few other things that we'll get into shortly. 

For this project, it definitely took a little while for things to get going and for me to feel comfortable  implementing various features. Once I got the ball rolling by looking at examples and playing around with things a bit, I became more comfortable trying new things and troubleshooting. Cheers!

## Serializers

In order for my Rails App to function as  Rails API, it needs to serve up JSON— that’s where serializers come in. 

I had to add serializers for three of my models, Workouts, Exercises, and WorkoutExercises. In each case I included only the the attributes I wanted returned with each serialized JSON object, and I also added associations to my serializers so that I can display these relationships with each JSON object. 


Once I did that, I needed to update my controllers so that JSON data can be rendered  and consumed later. I essentially used jQuery for these actions:

- exercises#create
- exercises#index
- exercises#show
- workouts#index
- workouts#show


For the #index actions, it was as simple as adding the following the the bottom of the controller action, here “@objects” is @exercises or @workouts, respectively.

```
respond_to do |f|
      f.html
      f.json {render json: @objects}
end
```


For the #show actions, I just added, with either @exercise, or @workout, the following:

```
respond_to do |f|
      f.html
      f.json {render json: @object}
end
```


Finally, for the #create action in the exercises controller, I just added the following:  

```
@exercise.save ? (render json: @exercise, status: 201) : (render :new)
```

If the form to create a new exercise submits and the exercise saves correctly, the JSON for that object is rendered and a status code of 201 (new resource successfully created) is used. If not, the page with the form is re-rendered. Ez pz!

----------------------
## Javascript: getting started (application.js)

To start, I added functions that add “listeners” for the specific events (typically click events) that I wanted to trigger some javascript in my application. I suppose I did this backwards, but I like to begin with the end in mind!

I initially had some difficulty getting functions to trigger at all, and it turned out to be a Turbolinks issue; Turbolinks prevents normal $(document).ready() events from firing on all page visits besides the initial load. The common solution to this seems to be to use the Turbolinks:load event to trigger other events. To implement this,  I chose to invoke the functions that add my listeners all at once in application.js. 


```
$(document).on('turbolinks:load', () => {
  addExerciseIndexListener()
  addExerciseShowListeners()
  addWorkoutIndexListener()
  addWorkoutShowListeners()
  hijackExerciseForm()
  hijackFilterForm()
})
```



### Breaking Down the Javascript functions (declared in exercises.js and workouts.js)

**addExerciseIndexListener()**

```
const addExerciseIndexListener = () => {
  $('a.all_exercises').on('click', (event) => {
     event.preventDefault();
     history.replaceState(null, null, 'exercises')
     getExercises();
  });
};
```

This function attaches a click event to one link (“View All Exercises”) in the nav bar, which has a class of “all_exercises”. When this is clicked, the default behavior (to route to the /exercises route) is prevented, and the function getExercises() is called.



**getExercises(muscle = null)**

```
const getExercises = (muscle = null) => {
  const form =
    `<h2>Filter exercises:</h2>
    <form action='/exercises' accept-charset='UTF-8' method='get'>
      <input class='filter' name='utf8' type='hidden' value='✓'>
      <select name='muscle_group' id='muscle_group'>
        <option value=''></option>
        <option value='Glutes'>Glutes</option>
        <option value='Abdominals'>Abdominals</option>
        <option value='Chest'>Chest</option>
        <option value='Quadriceps'>Quadriceps</option>
        <option value='Hamstrings'>Hamstrings</option>
        <option value='Calves'>Calves</option>
        <option value='Back'>Back</option>
        <option value='Shoulders'>Shoulders</option>
        <option value='Biceps'>Biceps</option>
        <option value='Triceps'>Triceps</option>
        <option value='Multiple'>Multiple</option>
      </select>
      <input type='submit' name='commit' value='Filter' class='filter' data-disable-with='Filter'>
    <form/>` // hidden to keep this short; it's just a form!
  fetch(`/exercises.json`)
  .then(response => response.json())
  .then(exercises => {
    debugger
    $('#app-container').empty();
    $('#app-container').append(form);

    const matches = muscle ? (exercises.filter(ex => ex.muscle_group === muscle)) : exercises;

    if (matches.length > 0) {
      matches.forEach(exercise => {
        const newExercise = new Exercise(exercise);
        const exerciseHTML = newExercise.formatIndex();
        $('#app-container').append(exerciseHTML);
      });
    } else {
        $('#app-container').append(`<h3>No Matching Exercises Were Found. Please try again!</h3>`)
    };
  });
};
```


This function does a few different things! Essentially, it is responsible for rendering the index resource for the exercise model. First the function makes a fetch request to  /exercises, and the response that is returned is serialized with .json() and returned as an array of JSON objects. 

Once that data is obtained. the function does a few different things to appropriately format the page:

1. Clear the page and append the exercise filter form to the page
2. If a muscle group has been selected and submitted with the filter form, only the JSON exercise objects with a matching muscle_group attribute will be selected; otherwise, the entire array with be iterated over. Each matching exercise is translated to a JS model object via the exercise class, and then the method .formatIndex() is called on each object to generate the html for each exercise, which is then appended to the page. If the filter is used and no exercises match, a message is appended to the page instead, telling the user no match was found. 


**addExerciseShowListeners()**

```
const addExerciseShowListeners = () => {
  $(document).on('click', '.show_exercise', function(event) {
    const id = $(this).attr('data-id');

    event.preventDefault();
    $('#app-container').html('');
    getExercise(id);
  });
};
```


This function attaches a click event to links for the exercises rendered in getExercises() above. Since those links don’t exist on Turbolinks:load, though, that presents an issue. This listener is attached to the document page/window such that when the individual exercise links (each with a class of “show_exercise”) is clicked, the default behavior (to route to /exercises/id) is prevented, and the data attribute “data-id” of the link is stored as a constant. The function getExercise(id) is then invoked, with the constant submitted as a parameter.


**getExercise(id)**

```
const getExercise = (id) => {
  fetch(`/exercises/${id}.json`)
   .then(response => response.json())
   .then(exercise => {
     const newExercise = new Exercise(exercise);
     const exerciseHTML = newExercise.formatShow();
     $('#app-container').empty();
     $('#app-container').append(exerciseHTML);
   });
};
```


This function is responsible for rendering the show resource for the exercise model. The function makes a fetch request to  /exercises/:id, and the response returns a serialized JSON object. This object (an exercise) is then translated to a js model object, formatted with the object method .formatShow(); after the page is cleared, the html generated by that method is appended to the page. 



**addWorkoutIndexListener()**


This functions similarly to addExerciseIndexListener(); it attaches a click event to one link (“View All Workouts”) in the nav bar, which has a class of “all_workouts”. When this is clicked, the default behavior (to route to the /exercises route) is prevented, and the function getWorkouts() is called. 


**getWorkouts()**


This function behaves similarly to getExercises(), only with the corresponding Workout data, JSON objects, JS model objects, etc. Unlike getWorkouts, it does not take an argument, as there is no filter form in the /workouts resource to contend with. 


**addWorkoutShowListeners()**


This function attaches a click event to links for the workouts rendered in getWorkouts() above in a fashion similar to that of addExerciseShowListeners(). This listener is attached to the document page/window such that when the individual workout links (each with a class of “show_workout”) are clicked, the default behavior is prevented, and the data attribute “data-id” of the link is stored as a constant. The function getWorkout(id) is invoked, with the constant submitted as a parameter.


**getWorkout(id)**


This function is responsible for rendering the show resource for the workout model and behaves similarly to getExercise(id), only with the corresponding Workout data, JSON objects, JS model objects, etc. 


**hijackExerciseForm()**

```
const hijackExerciseForm = () => {
  $('#new_exercise').submit(function(event) {
    const values = $(this).serialize();
    const posting = $.post('/exercises', values);

    event.preventDefault();
    posting.done(data => {
      const newExercise = new Exercise(data);
      $('#exerciseName').text(newExercise.name);
      $('#exerciseBody').text(newExercise.description);
      $('#new_exercise').trigger('reset');
    });
  });
};
```


The function above attaches a submit event to the form to create a new exercise. This button has a class of “new exercise”. When the form is submitted, the default behavior of the form is prevented, and the values of the form are serialized and sent via the jQuery $.post method to the Rails API backend. When that has been completed successfully, the API returns the serialized JSON exercise as a response. This response is then translated into a JS model object by means of the Exercise class. Underneath the exercise form are some empty html tags with classes of “exercise name” and “exercise body”. The text for each tag is then set based on the attributes (e.g. name, description) of the JS exercise object with the function .text(), and the form is cleared with .trigger(‘reset’), for a nicer user experience. 



**hijackFilterForm()**

```
const hijackFilterForm = () => {
  $('.filter').on('click', function(event) {
    const muscleGroup = $("#muscle_group").val();

    event.preventDefault();
    getExercises(muscleGroup);
  });
};
```

So, it occurred to me at some point in my project that I had created a formatting issue for myself. I had some resources (like the exercise index resource), that could be rendered two ways: with the Rails view or with jQuery. While this wasn’t…. terrible, the two pages looked different, which would be off-putting to a user. So, aside from tiny formatting things that I won’t go into great detail about here, I had one big issue: I had a form that allows users to filter exercises based on muscle group, and I needed that form to 1) display when the exercises are rendered with jQuery, and 2) behave normally when rendered this way. 

The function hijackExerciseFilterForm() attaches a click event to the exercise filter form, which has a class of “filter” on its submit button whether the form is displayed in the Rails view or with jQuery. When a user selects a muscle group and hits the submit button, the default behavior of the form is prevented, and the value of the user’s selection is stored as a constant, then passed as an argument to getExercises(). Success!

Thanks for reading! If you'd like to check out more of the code for this project, like the corresponding JS classes, check out the repo [here](https://github.com/ddeleon267/exercise-me)!
