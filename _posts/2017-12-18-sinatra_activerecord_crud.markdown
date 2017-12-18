---
layout: post
title:      "Sinatra ActiveRecord CRUD"
date:       2017-12-18 18:25:35 +0000
permalink:  sinatra_activerecord_crud
---



Obligatory intro stuff about this lab. Mention the stuff that you're given off the bat

--------------------------------------------
## CREATE Action

![Imgur](https://i.imgur.com/ANxQnm2.png)



#### "Creates a New Blog Post" and "Redirects to '/posts' "

The first two tests are testing the CRUD **Create** Action.

To create a blog post, you first need a means to create one -- your post model. This is easy enough to do; just add the file post.rb to your Models folder. Create a class Post that inherits from ActiveRecord::Base.
<br>
```
class Post < ActiveRecord::Base
end
```

An easy win. You don't need to add anything to your Post class aside from that-- ActiveRecord does all the heavy lifting for you!

<br>

While you now have a means to create new posts, you don't have a way to save them. You need to create a database so that your posts can persist and you won't drive yourself bananas wondering why your post data JUST WON'T DISPLAY NO MATTER WHAT YOU DO (I speak from experience).

For your own sanity, create a migration that sets up your posts table-- and then migrate that goodness!

```
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :name
      t.string :content
    end
  end
end
```

Now you can create some posts that will actually hang around in your program. 

<br>

To create a blog post that your user can actually see, you need a controller route that gets '/posts/new' and renders the new.erb view. That file doesn' exist yet, so add it to your views folder. Set up the controller action that renders that page:

```
  
  get '/posts/new' do 
    erb :new
  end
```

Currently this view doesn't do anything. It needs to contain a form that POSTs (this is the form method) to a controller action, /posts. To create a post you will need a post name and post content, so you will need input tags with name values of "name" and "content", respectively. For the "submit" button you will need a final input with a type and value of "submit."

```
<form method="POST" action="/posts">
  Name:<input type="text" name="name">
  Content:<input type="text" name="content">
  <input type="submit" value="submit">
</form>

```

<br>
We're POSTing this data to the controller action '/posts', so that action needs to be able to create a post using CRUD conventions.

```
  post '/posts' do 
    @post = Post.create(params) ##this is where we make new posts that save to our database!
  end
```

<br>
The create method is provided by Active Record and allows us to instantiate and save the new post instance to the database. The params argument allows us to assign the post's attributes using the data stored in the params hash, and saving the newly created post object in the instance variable @post means that we have it on hand to use later.

<br>

What do you want to do after you create a new post? You want to see the post that you have created! We need a controller action that 'gets' /posts and displays your list of posts (which for now is just the one post). This is done in the index view by convention. 

```
  get '/posts' do 
    erb :index
  end
```

Now you just need to teach the '/posts' view (index.erb) how to display your page data, which is simple enough in Ruby, although you will need appropriate erb tags:

```
<%=@post.name%>
<%=@post.content%>

```

<br>
The second test is looking for the post '/posts' controller action to redirect to '/posts'.  Now that our index page exists and we have a controller action that renders that page, it makes sense to add a line into our post '/posts' action to redirect to that page:

```
  post '/posts' do 
    @post = Post.create(params)
    redirect to '/posts'    #this is the line we are adding in
  end
```
<br>
So now our Blog Post App has a successful CREATE action --> it can create a new blog post and redirect to '/posts' to show us the attributes of our newly created post. At this point we have passed the first two tests! And yet, we still have so far to go.
<br><br><br>

-------------------------------------------

## READ Action
<br>
![Imgur](https://i.imgur.com/PbpXoZh.png)

The next three tests are testing for the CRUD 'READ' action. We need two different controller actions: 'show' and 'index'. 


### Index Action
The index action allows a user to see all of the posts that have been created. This requires a slight modification of our get '/posts' action and our index view. If the intent is to render data for all of our posts in the index view, you first need access to all of the posts, so you can add a line in to the get '/posts' action that will store all of the posts in an instance variable:


```
get '/posts' do 
    @posts = Post.all #this is the line we are adding-- now we can store all of our saved post objects!
    erb :index
  end
```

#### "Responds with a 200 Status Code"

This is going to 'break' our index view (temporarily) as it is currently referencing a single post and not all of the posts. Deleting that content will allow you to pass the previous test, but you can easily modify the index view so you you can pass both that test and the next one:

#### "Displays all the Blog Posts"

Adjust your code in index.erb to iterate through @posts and display the name and content of each post:

```
<%@posts.all.each do |post|%>
  <%=post.name%>
  <%=post.content%>
<%end%>

```

Now the user can see all of your posts! 


### Show Action

If you want to see a single post's page, you will need to create a 'show' controller action that gets a particular post by id-- this ensures that this action is dynamic, and you don't need to create a separate controller action for each post. 


#### "Show Page Responds with a 200 Status Code"
Create a file show.erb in your views folder-- this will display the data for a single post to the user. Create a controller action that correctly routes to and renders the page:
```
get '/posts/:id' do
    erb :show
  end
```

#### "Show Page Displays the Post's Name" and "Show Page Displays the Post's Content"

To display an individual post in the show view you need to first access it in the databse. Add line to the controller action that allows you to find the given post by id using the AR method .find_by_id. This method will take in the id of the given pose by accessing  the id key of the params hash.  Store that post instance in an instance variable so you can access it elsewhere. 

```
get '/posts/:id' do
    @post = Post.find_by_id(params[:id]) #this is the line we are adding
    erb :show
  end
```

You can use the data stored in @post to to display the post's name and content n show.erb using some simble Ruby and erb tags:

```
<%=@post.name%>
<%=@post.content%>

Voila! Very fancy.
```
-------------------------------------------
## UPDATE Action


![Imgur](https://i.imgur.com/RUeIxYN.png)

#### "Responds with a 200 Status Code"

The Update 
	
	The Update CRUD action corresponds to the edit controller action and view. You need to navigate successfully to the edit view for a specific post (which you access by id) and render that page, which will contain a form that allows you to input new/information about the post. You'll then have to update the show and index  views so that the posts's new information  displays correctly. 
	 
	
Create a file edit.erb in your views folder. Then create a controller action that navigates to the edit route and renders the view-- you want something that looks like /posts/id/edit but you want it to be dynamic, so use /posts/:id/edit, where :id stands in for ______. You'll need to actually access the post object first  to do this successfully, so use ActiveRecord's #find_by_id method to grab the post using the value of params[:id] an an arguent. Store that in an instance variable so you have it for later.
	
```
get '/posts/:id/edit' do
    @post = Post.find_by_id(params[:id])
    erb :edit
  end
```

You won't have any data to display yet in your edit view, but you'll be able to render the page successfully.

#### "Displays the Existing Object in the Edit Form"
	
The edit view should contain a form to update a specific blog post and POSTs (this is the form method) to a controller action, patch '/posts/:id'.
You'll need to make sure the edit form includes:  What is happening here??????

<form method="post" action="/posts/<%=@post.id%>">
  <input id="hidden" type="hidden" name="_method" value="patch">*****
  Name<input type="text" value="<%=@post.name%>" name="name">
  Contents<input type="text" value="<%=@post.content%>" name="content">
  <input type="submit" value="submit">
</form>

#### "Saves Edits to a Blog Post" and "Submits the Form Via a Patch Request"

```
patch '/posts/:id' do  #updates a post
    @post = Post.find_by_id(params[:id])
    @post.name = params[:name]
    @post.content = params[:content]
    @post.save
    
  end
```

#### "Redirects to '/posts/:id' " 

After you update a post's information you'd probably want to see the page for that individual post to make sure everything looks good. Our test is looking for our patch request to redirect to the '/posts/:id' view, so add a line to render that view:


```
patch '/posts/:id' do  #updates a post
    @post = Post.find_by_id(params[:id])
    @post.name = params[:name]
    @post.content = params[:content]
    @post.save
    erb :show  #this is the line we are adding
  end
```
	
	
-------------------------------------------
## DELETE Action

![Imgur](https://i.imgur.com/0dwdAAp.png) 

#### "Responds with a 200 Status Code"

If you've been following along so far you we see that this test already passes, as it is is looking for a successful loading of /posts/:id,  which we have working from a previous example

#### "Deletes a Blog Post From the Database" and "Submits the Form via a Delete Request"

To delete a blog post we need a 'delete' controller action that can both access and delete an individual post. To initiate this, we need a delete button on the show page for an individual post -- just add it underneath the code that's already there. This form should POST to the '/posts/:id/delete' controller action.
	
```
#just add this code to the bottom of your show.erb file
<form method="post" action="/posts/<%=@post.id%>/delete">
  <input id="hidden" type="hidden" name="_method" value="delete">

  <input type="submit" value="delete">
<form>
```

Let's go ahead and create this action-- it needs to identify and access the individual post (which we've seen how to do alread) that needs to be deleted, and then delete it using ActiveRecord's #delete method

`delete '/posts/:id/delete' do
    @post = Post.find_by_id(params[:id])
    @post.delete
  end`


#### Displays a View Telling Us Which Post Was Deleted"
We need to render a delete.erb view which confirms that the post has been deleted, so go ahead and create a deleted.erb. Add a line to render that page in the controller action :We need to display a message telling us which post was deleted

```
delete '/posts/:id/delete' do
    @post = Post.find_by_id(params[:id])
    @post.delete
    erb :deleted #this is the line we are adding
  end
```

Easy enough with ruby and erb!

`<h1> <%= @post.name %> was deleted</h1>`

Now if you go ahead and run your tests, you'll see that everything is passing!
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
