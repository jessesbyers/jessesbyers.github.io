---
layout: post
title:      "MyDIY Rails Project "
date:       2020-02-12 20:27:43 -0500
permalink:  mydiy_rails_project
---


For my Rails Project assessment, I created a web application called MyDIY. You can watch a[ video walkthrough](https://drive.google.com/file/d/19It01i7Q-IBm-9QdxjmBwTnG8iEgDPvl/view?usp=sharing), and view it on [github](https://github.com/jessesbyers/MyDIY).

> MyDIY is a collaborative project planning and blogging app for Do-It-Yourself-ers, House-Flippers, and Property Managers. Each User can work on mulitple projects and collaborate in different ways with other users. Users can create project goals, updates, resources, and post images as the work is being done. 
> 

## The Challenge
The requirements for this project were significant, and required managing complex relationships and nested forms and resources. It was fairly easy to build in the required associations, however, the main challenges I encountered were related to setting up and managing collaborations amongst users and managing different levels of privileges based on a user's role.

![Imgur](https://i.imgur.com/0vDCMgR.png)

### Collaboration Details
I created collaborative relationships in my project through a Collaboration model that served as a join table between the User model and the Project model. 

![Imgur](https://i.imgur.com/Y13suRm.png)

The Collaborations table included project_id, user_id, and a role, and this allowed users to have multiple projects, and projects to have multiple users, with different roles for each project.

![Imgur](https://i.imgur.com/P63fW9m.png)

### Collaboration Roles
A User becomes a Primary Project Owner automatically when they create a Project. The Primary Project Owner can then add and edit Collaborations with other Users.

![Imgur](https://i.imgur.com/rNACY4f.png)

## Managing Collaboration
As I quickly learned, managing Collaborations is very complex. It involves managing access to individual pages through the controllers, and managing which buttons users can see on specific views.

### Models and Controllers
In the models, I created scope methods that could be called throughout the application to identify and filter users based on their roles. For example, the following methods return a collection of projects in which a user has a certain role.

![Imgur](https://i.imgur.com/PmYIRQX.png)

These methods can then be used in the controller to manage access to certain actions and views:

![Imgur](https://i.imgur.com/iMu5WH8.png)

### Views
In the views, there were many instances in which I had to use logic in the views to display different options to different Users based on their role. For an example, a Primary Project Owner should have options to edit the project, add collaborators, and add goals on the Project show page, but a View Only collaborator should only be able to see links to view the project and goal details. This led to a lot of conditional statements that pulled in the model methods described above. Initially, a view would be very difficult to read because of all of the logic. 

![Imgur](https://i.imgur.com/uadDpll.png)

Once I got the views working as planned, I got to work on refactoring.



## Refactoring Views with Partials and Helpers
The task of refactoring seemed colossal. I started by moving chunks of repeated code into partials first, and then tackled more of the logic using helpers, sometimes in conjuntion with the partials I had already created.

### Partials
The most obvious first step in tackling the views was to address the forms. I created an errors partial to display error messages and a form partial for each model to render form content more cleanly and efficiently. I was also able to create partials for some common actions such as delete or edit buttons.

### Helpers 
I then created helper methods to render HTML from Ruby code to address some of the conditional logic in the views. For example, on the Goals index page, there were three sections that included conditional logic for displaying the latest update, resource, and image for the goal. I was able to create helper methods which took care of some of the logic, and used them in a partial to render the details of each latest activity section:
![Imgur](https://i.imgur.com/p6eiZqy.png)


![Imgur](https://i.imgur.com/RzsV7UQ.png)


I then called the partial into the view:

![Imgur](https://i.imgur.com/cqjSU8p.png)

In the end, this created a clean display of latest updates in the browser, with all of the logic hidden from view.

![Imgur](https://i.imgur.com/XvMMa6Y.png)

## Lessons Learned
Collaboration is complex! Looking back at the project, knowing what I know now, there are some changes I would make for my next attempt at collaboration.

1. Instead of using role names such as "Primary Project Owner", I would assign numerical values to each role. Then I would be able to use simple operators in the methods to control access (if @collaboration.role >3 ....)

2. I would explore using a gem such as Can Can Can to help manage user roles and privileges. While I learned a lot through creating the roles manually, I did spend the majority of my project time figuring out the ins and outs of access restrictions, testing, fixing bugs, and re-testing.

3. While refactoring code will often make it cleaner and easier to read, there is a point at which too much refactoring makes the code harder to read. It is important to strike the right balance in order to write clean code that is also highly understandable.
4. Testing User roles and privileges is very time-consuming and needs to be done repeatedly to make sure there are no unintended consequences of changing the code to address a particular bug. This is a case in which Capybara feture tests could be very useful, and I would like to explore these tests more so I could implement them the next time I develop a project that relies on collaborative relationships.

All in all, I loved building the project and working through the challenge of incorporating user roles and collaborations. I look forward to using MyDIY for my own home projects, and hope you'll check it out too! You can watch a[ video walkthrough](https://drive.google.com/file/d/19It01i7Q-IBm-9QdxjmBwTnG8iEgDPvl/view?usp=sharing), and view it on [github](https://github.com/jessesbyers/MyDIY).





