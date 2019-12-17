---
layout: post
title:      "Bird Log: My First Sinatra Project"
date:       2019-12-17 14:33:04 -0500
permalink:  bird_log_my_first_sinatra_project
---


I created Bird Log, a bird watching web app, for my first Sinatra Project. You can check it out on github [here](https://github.com/jessesbyers/bird_log) and watch a  video walkthrough [here](https://drive.google.com/file/d/1fCaFbrCTTwZXgaMz6NLF7vbsmQyA0lUf/view?usp=sharing). 

<a href="https://imgur.com/57Iovui"><img src="https://i.imgur.com/57Iovuil.png" title="source: imgur.com" /></a>

## Process
My goal was to create a fully-functioning CRUD app in 5 working days. Looking back to my CLI project, I had a lot of time, and as a result, was not as focused in my programming as I could have been. This time around,  I limited myself to five eight-hour days so that I could focus on efficiency and making continuous forward progress. In the end, I think these constraints helped make me a more purposeful programmer.

### DAY 1
#### Morning: Make a plan 
First, I planned out the overall goal and functioning of my bird watching app, planned out my models and associations, and did a test run of scraping the Audubon Society website to make sure my plan was feasible.

#### Afternoon: Build a File Structure 
Next, I created my repo and github and focused on building out a file structure so that my models, views, and controllers were organized.  I started building out my models (User, Sighting, and Bird) and ran migrations to create tables for each model. 
<a href="https://imgur.com/sFu5ACv"><img src="https://i.imgur.com/sFu5ACv.png" title="source: imgur.com" /></a>
<a href="https://imgur.com/7BJksJz"><img src="https://i.imgur.com/7BJksJz.png" title="source: imgur.com" /></a>
<a href="https://imgur.com/T5PuCla"><img src="https://i.imgur.com/T5PuCla.png" title="source: imgur.com" /></a>



### DAY 2
#### Morning: Models and Controllers 
Next, I created the application controller and the controllers for each model. Most of this time was spent focused on the users controller to ensure that users could sign up for and log into secure accounts.

#### Afternoon: Views and Testing 
I drafted some very rudimentary views for each model. Most views just included directions to myself on what content would eventually live there, and who would be able to access it. This step allowed me to start testing the routes in each controller. By this point, I could log in, create content, and navigate between screens. The morning was spent testing features using pry, tux, and shotgun, finding and fixing bugs, and solidifying user permissions for each route and view.

### DAY 3
#### Morning: Testing, Sharing and  Feedback 
Amazingly, after 3 days of work, I had a fairly functional CRUD app that I could share with a classmate. I shared my app and demonstrated the functions, and got some feedback and ideas on how to improve navigation and secure some of the views for users. 

#### Afternoon:  Exporing CSS possibilities 
Having never used CSS on a project before, I took some time to watch video walkthroughs on Grid Flexbox and started to sketch out what each view should really look like. I decided to use Grid because of its flexibility, and try to create a a simple tile structure on each view page.
<a href="https://imgur.com/ragmt0x"><img src="https://i.imgur.com/ragmt0x.png" title="source: imgur.com" /></a>


### DAY 4
#### Morning: First attempts at CSS 
I bumbled around for awhile just figuring out how CSS would fit into my project structure. I created headers and footers and added some styling to the layout.erb file, and then spent a lot of time troubleshooting how to correctly link a stylesheet from a public folder to the layout.erb folder.

#### Afternoon: Finished first draft CSS 
The bulk of this time was spent creating parent and child  containers in each view to apply a grid layout on each page.
<a href="https://imgur.com/CZ0aJAX"><img src="https://i.imgur.com/CZ0aJAX.png" title="source: imgur.com" /></a>

### DAY 5
#### Morning: Final Testing,  Bug Fixes, and Formatting 
Finally, it was time to apply colors, fonts, and make tweaks on sizing of images and text. Along the way, I discovered a few minor bugs in links and routes and was able to fix those quickly.

#### Afternoon - Final Details 
Finally, it was time to create my video walkthrough and write this blog post, and submit the project.

<a href="https://imgur.com/Pv1I1nK"><img src="https://i.imgur.com/Pv1I1nK.png" title="source: imgur.com" /></a>

## Lessons Learned
### Model Associations
The most important starting point for a project is modeling the associations between classes and solidifying the attributes of each class or model. Once this planning is done, the actual programming of model classes using Active Record goes very quickly.

### User Accounts and Security
Creating a secure site means more than just requiring a password. We need to use methods to secure routes as well as views. Sometimes, we just need to check whether a user is logged in, but at other times, we need to make sure a user is logged in, and that the current user is authorized to do actions on a particular resource.

### Styling
There is most definitely an order of operations in styling a web application. At first I started thinking about colors and fonts, but quickly realized that those aspects should be designed after a basic layout is achieved.
<a href="https://imgur.com/ej2hGzy"><img src="https://i.imgur.com/ej2hGzy.png" title="source: imgur.com" /></a>


## Future Enhancements
Since I finished this project early, my plan is to create a second Sinatra project for extra practice. However, if I do come back to this project in the future, here is wat I would tackle next:

1. Create dropdown or checkbox menus in the New Sighting Form for every bird species already added to the database.
2. Create dropdown or checkbox menus in the New Sighting Form for every location that the current user has added to the database.
3. Reformat Sign Up and Log In pages to be more visually appealing.
4. Vertically align all images in the grid.
5. Make the app responsive for mobile devices.




