---
layout: post
title:      "How To...Rails: Validations"
date:       2020-01-28 18:57:06 +0000
permalink:  how_to_rails_validations
---

*Validations in Rails allow us to protect the data that is entered into our database by checking whether it meets certain criteria or requirements. This is important because savvy hackers could edit the HTML on our form views and create or modify fields that should not be available to them. To prevent this, we can use common validation methods that are included in ActiveRecord, create custom validation methods, and display error messages and redirects when a user tries to enter invalid data.*

### Contact List Example
For this post, I’ll continue using the Contact List app example from my previous posts on  [Rails CRUD](https://jessesbyers.github.io/how_to_rails_basic_crud_restful_routes_and_helper_methods) and [Complex Associations](https://jessesbyers.github.io/how_to_rails_complex_associations_nested_forms_and_form_helpers). In this example, the data is used by the Fire Department in order to rescue both people and their pets. Therefore, it is vital that only good data is entered into the database so that the data on each household will be useful and accurate.

## Validation Methods in ActiveMethod
### 1. Add the method underneath the class name in the model.
Some common validations let you check that a certain attribute is present, unique, or is a number. You can further customize the validation by including length, whether certain characters are included or excluded, and more. While the methods automatically generate error messages, you can also add a custom error message with the validation if you would like.

![Imgur](https://i.imgur.com/rRAO7Gi.png)

### 2. Update the controller to run validations
After the validations are set in the model, they will only be called if you use the .valid? method, or if you try to save or update an instance (by calling .create, .save, or .update). You must update the create action in the controller to check if the instance is valid. If the instance is valid and the save is successful, it will redirect to the contact show page. If not, it will re-render the form.

![Imgur](https://i.imgur.com/n4iyLTd.png)

### 3. Add error messages on form
Whenever an instance is invalid, it generates a hash that includes the attributes of the model with corresponding error messages. You can add a section at the top of the form to print out the content of those messages if, and only if, the record is invalid. 

![Imgur](https://i.imgur.com/aC4mSQw.png)

Since the new action is set with the instance variable @contact, it will print out those messages if there are any, and that section will not render at all if there are no errors. The form will also keep the valid values that were already added to the form so they will not need to be re-entered.

### 4.  Update CSS to highlight error fields

![Imgur](https://i.imgur.com/k6qAcfB.png)

Finally, you can add CSS styling to highlight the error fields so it will be easy for the user to find and correct the values. The re-rendered form will include a list of all of the invalid data, and highlight the fields to be corrected.

![Imgur](https://i.imgur.com/H0WtXQ8.png)


## Custom Validation Methods
The validation methods in ActiveRecord cover most of the common scenarios that might want to validate for. However, you can also create custom validation methods within the model to account for these uncommon use cases.

### 1. Create a ruby method within the class
Let’s say we want to validate that the address is from the local state and zip code, in order to prevent residents of other states or towns to be entered into the database. We can create an instance method using Ruby logic to identify the condition that is not allowed, and identify the error message that should be printed if that condition is true.

### 2. Add a validation line at the top of the model
In this case, instead of using “validates”, use “validate” with the custom method name.

![Imgur](https://i.imgur.com/vTjMfRD.png)

### 3. Update the controller and views
The rest of the process will be exactly the same as when you use built-in validation methods, as described above.

![Imgur](https://i.imgur.com/OwWQy8P.png)

Validations are pretty straightforward, and more detail can be found in the [Active Record Validations guide](https://guides.rubyonrails.org/active_record_validations.html).


### How To Series
*In this series of How To posts, I will be summarizing the key points of essential topics and illustrating them with a simple example. I’ll briefly explain what each piece of code does and how it works. Stay tuned as I add more How To posts in the series each week!*

* [Post 1: How To...Rails: Basic CRUD, RESTful Routes, and Helper Methods](https://jessesbyers.github.io/how_to_rails_basic_crud_restful_routes_and_helper_methods)
* [Post 2: How To...Rails: Complex Associations, Nested Forms, and Form Helpers](https://jessesbyers.github.io/how_to_rails_complex_associations_nested_forms_and_form_helpers)



