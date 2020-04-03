---
layout: post
title:      "Bird Log: A Sinatra Project"
date:       2019-12-17 14:33:04 -0500
permalink:  bird_log_my_first_sinatra_project
---


I created Bird Log, a bird watching web app, for my first Sinatra Project. 

[Check it out on Github](https://github.com/jessesbyers/bird_log) and [Watch a video walkthrough](https://drive.google.com/file/d/1fCaFbrCTTwZXgaMz6NLF7vbsmQyA0lUf/view?usp=sharing). 

<a href="https://imgur.com/57Iovui"><img src="https://i.imgur.com/57Iovui.png" title="source: imgur.com" /></a>
## Process
My goal was to create a fully-functioning CRUD app in 5 working days. Looking back to my CLI project, I had a lot of time to commit to that project, and as a result, I was not as focused in my programming as I could have been. This time around,  I limited myself to five eight-hour work days so that I could focus on efficiency and making continuous forward progress. In the end, I think these constraints helped make me a more organized and purposeful programmer.

### DAY 1
#### Morning: Make a plan 
First, I planned out the overall goal and functioning of my bird watching app, planned out my models and associations between them, and did a test run of scraping the Audubon Society Online Bird Guide to make sure my plan was feasible.

#### Afternoon: Build a File Structure 
Next, I created my repository on github and focused on building out a file structure so that my models, views, and controllers were organized.  I then started building out my models (User, Sighting, and Bird) and ran migrations to create tables for each model. 
<a href="https://imgur.com/sFu5ACv"><img src="https://i.imgur.com/sFu5ACv.png" title="source: imgur.com" /></a>
<a href="https://imgur.com/7BJksJz"><img src="https://i.imgur.com/7BJksJz.png" title="source: imgur.com" /></a>
<a href="https://imgur.com/T5PuCla"><img src="https://i.imgur.com/T5PuCla.png" title="source: imgur.com" /></a>



### DAY 2
#### Morning: Models and Controllers 
Next, I created the application controller and the controllers for each model. Most of this time was spent focused on the users controller to ensure that users could sign up for and log into secure accounts.

#### Afternoon: Views and Testing 
I drafted some very rudimentary views for each model. Most views just included directions to myself on what content would eventually live there, and who would be able to access it. This step allowed me to start testing the routesand redirects in each controller. By this point, I could log in, create content, and navigate between screens. I tested features and object relationships using pry, tux, and shotgun; uncovered and fixed some bugs; and solidified user permissions for each route and view.

### DAY 3
#### Morning: Testing, Sharing and  Feedback 
At this point I had a fairly functional CRUD app that I could share with a classmate. I shared my app and demonstrated the functions, and got some feedback and ideas on how to improve navigation and better secure some of the views for users. 

#### Afternoon:  Exporing CSS possibilities 
Having never used CSS on a project before, I took some time to watch video walkthroughs on Grid and Flexbox and started to sketch out what each view should really look like. I decided to use Grid because of its flexibility, and planned to create a simple tile structure on each view page.
<a href="https://imgur.com/ragmt0x"><img src="https://i.imgur.com/ragmt0x.png" title="source: imgur.com" /></a>


### DAY 4
#### Morning: First attempts at CSS 
I bumbled around for awhile just figuring out how CSS would fit into my project structure. I created headers and footers and added some general styling to the layout.erb file, and then spent a lot of time troubleshooting how to correctly link a stylesheet from a public folder to the layout.erb folder so that it would apply to all views.

#### Afternoon: Finished first draft CSS 
The bulk of this time was spent creating parent and child  containers in each view to apply a grid layout on each page. This became complicated very quickly, and made me realize why many people create separate stylesheets for each view.
<a href="https://imgur.com/CZ0aJAX"><img src="https://i.imgur.com/CZ0aJAX.png" title="source: imgur.com" /></a>

### DAY 5
#### Morning: Final Testing,  Bug Fixes, and Formatting Details
This was finally the time to apply colors, fonts, and make tweaks on sizing of images and text. Along the way, I discovered a few minor bugs in buttons,  links, and routes and was able to fix these fairly quickly.

#### Afternoon - Final Details 
I created my video walkthrough and write this blog post, and submitted the project.

<a href="https://imgur.com/Pv1I1nK"><img src="https://i.imgur.com/Pv1I1nK.png" title="source: imgur.com" /></a>

## Lessons Learned
### Model Associations
The most important starting point for a project is modeling the associations between classes and solidifying the attributes of each class or model. Once this planning is done, the actual programming of model classes and tables using Active Record goes very quickly.

### User Accounts and Security
Creating a secure site means more than just requiring a password. We need to use methods to secure routes as well as views. Sometimes, we just need to check whether a user is logged in, but at other times, we need to make sure a user is logged in, and that the current user is authorized to do actions on a particular resource.

### Styling
There is most definitely an order of operations in styling a web application. At first I started thinking about colors and fonts, but quickly realized that those aspects should be designed after a basic layout is achieved. For my next project, I plan to use a CSS framework such as Bootstrap to further explore the possibilities in this area. I also learned the value of separating css stylesheets for each view, rather than putting them all in a single stylesheet.
<a href="https://imgur.com/ej2hGzy"><img src="https://i.imgur.com/ej2hGzy.png" title="source: imgur.com" /></a>


## Future Enhancements
Since I finished this project early, my plan is to create a second Sinatra project for extra practice. However, when I do come back to this project in the future, here is wat I would plan to tackle next:

1. Create dropdown or checkbox menus in the New Sighting Form for every bird species already added to the database.
2. Create dropdown or checkbox menus in the New Sighting Form for every location that the current user has added to the database.
3. Reformat Sign Up and Log In pages to be more visually appealing.
4. Vertically align all images in the grid.
5. Make the app responsive for mobile devices.

[Check it out on Github](https://github.com/jessesbyers/bird_log) and [Watch a video walkthrough](https://drive.google.com/file/d/1fCaFbrCTTwZXgaMz6NLF7vbsmQyA0lUf/view?usp=sharing). 




