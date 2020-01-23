---
layout: post
title:      "How To...Rails: Basic CRUD, RESTful Routes, and Helper Methods "
date:       2020-01-16 10:55:25 -0500
permalink:  how_to_rails_basic_crud_restful_routes_and_helper_methods
---


*In this series of How To posts, I will be summarizing the key points of essential topics and illustrating them with a simple example. I’ll briefly explain what each piece of code does and how it works. Stay tuned as I add more HOW TO posts in the series each week!*

## Contacts List Example 
To illustrate how Rails CRUD actions work, I’ll use a simple Contact List app as an example. Imagine that a User has many Contacts and a Contact belongs to a User. Each Contact has a name, address, phone number, and email address (all string data types). This post will only focus on the code around the Contact Model, Views, and Controller.

### A. Creating a New Rails App and Generating a Resource

**1. Create a new Rails app** by typing rails new in the Terminal. This creates the basic file structure and dependencies needed for a Rails app. 
```
 rails new contact_list 
```

**2. Create the Contact Resource** by using the rails generate command . By generating a resource, you will have the full suite of CRUD actions available for your model. It will auto-generate your migration file to create a table, a model file inheriting from ActiveRecord, a views directory, the resource call in the routes.rb file, among other files. To generate the resource, use the generate resource command with the model name and the attributes names and data types. After running the migration, your database will be set up for you.
```
 rails generate resource Contact name:string address:string phone:string email:string
```

```
rake db:migrate
```

![](https://i.imgur.com/1rZ3nFa.png/)

![](https://i.imgur.com/gTpXm5u.png)

![](https://i.imgur.com/Ye5QYr0.png)

### B. Testing Out Model Methods and Creating Seed Data

**3.** At this point, it would make sense to **set up the associations** between models (has_many and belongs_to, in this case). However, since this post is only focused on a single-model example, we will move on to creating test objects and working with the data. 

**4.** Now is a great time to **open up the console** and play around with creating contacts to make sure object instances are being created and saved without a hitch. 
```
rails c
```

Next, create a few instances in your seeds.db file and use the rake task to seed your database, so you have data to work with once you open up your server. Check to make sure the seed data worked properly by calling Contact.all in the console.
```
rake db:seed
```

![](https://i.imgur.com/ahbmrz5.png)

### C. Exploring, and Customizing Route Conventions

**5. Check your routes.**  Since Rails is so focused on conventions, you are already set up with a functioning routes.rb file for a general Contact model with all 7 RESTful routes. You can check your routes by typing rake routes into the terminal.
```
rake routes
```

![](https://i.imgur.com/Q0UgIb7.png)

However, if you wanted to customize this, you could replace resources :contacts with more specific or customized code. For example, you could use this code snippet if you only needed some of the routes:
```
 resources :contacts, only: [:show, :new, :create, :edit, :update]
```

Or you could use something like this if you wanted to use customized naming conventions:
```
 get '/contacts/:id', to: contacts#show', as: contact_details
```

In most cases, you can just use the auto-generated route macro.

![](https://i.imgur.com/uYx2ef5.png)

### D. Building out RESTful Routes in the Model Controller

**6. Build out controller actions for each of the RESTful Routes.** Explore the completed code below. This example uses Strong Params conventions by requiring that the contacts key is present in the params hash, and permitting only the listed attributes to be used in creating new contacts and saving them to the database. The private contact_params method allows us to secure the data we allow into our database.

![](https://i.imgur.com/ItwsXUW.png)

This code can be refactored using the private set_contact method to eliminate repetition in setting the instance variable using the find method.

![](https://i.imgur.com/MrRbCKp.png)

### E. Creating View Templates for our RESTful Routes

**7. Build out the Index and Show views** in the contacts directory. The Index and Show pages are pretty straightforward, and can use the instance variables that were set in the controller to show information about the individual contacts on each page. 

![](https://i.imgur.com/UAcVSDo.png)

The link_to and contact_path methods are used to create hyperlinks on the index page. They will auto-generate the appropriate hyperlink HTML tags for you.

![](https://i.imgur.com/yfp87pO.png)

**8. Create the New and Edit forms** using the form_for method. This uses FormBuilder to auto-generate form functionality for us. Both forms can actually have the same content, and FormBuilder will automatically detect which route to direct the params to, and whether the information should be sent as a post or patch request.

![](https://i.imgur.com/IB0m89h.png)

### F. Final Details

**9. Make this app more user-friendly and look presentable!** Add basic navigational links, including Edit and Delete buttons on the show page. Update the HTML and CSS to make it stylish, and you’ll have a simple Rails example that has all of the basic CRUD functions, RESTful Routing, and uses Helper Methods to keep the code clean and DRY.

*Stay tuned for the next How To post next week!
*
