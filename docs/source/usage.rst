Usage
=====

.. _installation:

Installation
------------

To use Project Master, first install it from the Github repository:

.. code-block:: console

   git clone https://github.com/setap5d/coursework.git

Definitions
-----------

A. Project

A project is an organised effort with a specific objective or set of objectives to be achieved. 
It is initiated and undertaken by one or more individuals and can be broken down into 
smaller, manageable tasks.

B. Task

A task is a specific and measurable unit of work within a project. It represents an individual 
objective contributing to the overall project goals. Tasks can be assigned to zero or more 
team members.

C. Team Member

A team member is an individual actively participating in the collaborative effort of a project. 
Each team member contributes their skills and expertise toward the completion of tasks.

D. Tickets

A ticket is a comment that can be written on any task and viewed by other team members including 
those who are not assigned to a task. The purpose of a ticket is to get help from someone who is not 
assigned to a task. 
It should provide information to the reader about what the problem is, and any other details that 
the writer deems necessary (steps to solve, attempts to fix, etc.)
Once the problem a ticket describes has been fixed, it should be able to be marked as resolved.


Be able to manage multiple projects at once
----------------
Users will be able to create new projects (see A).

Users will be able to view multiple assigned projects at any one time.

Users will be able to change the name, deadline and assignees.

Users will be able to delete projects.

Projects will have a title (string), deadline (date), description 
(string), and list of team members (see C) (list).

Consequences or side-effects: 

If a user has too many projects, it could cause the application to slow 
down on the projects page due to the number of widgets being displayed to them.

Storing more projects on our servers would cost more money as we store more data.

Be able to manage multiple tasks within projects
------------------------------------------------

Tasks (see B) will have a title (string), deadline (date), description 
(string), none or many assignees to that task and store zero or many tickets.

Project leaders will be able to create new tasks.

Project leaders will be able to change the title, deadline, description 
and assignees.

Consequences or side-effects: 

If a user has too many tasks, it could cause the application to slow down 
on the projects page due to the number of widgets being displayed to them

Storing tasks on our servers may cost more money as we store more data

Be able to manage user profiles within the application
------------------------------------------------------

Team members (see C) will have a name (string), email (string), and 
skills (optional string) associated with them.

Team members will be able to optionally store phone number 
(float) and profile picture (image).

Team members will be able to view stored data.

Team members will be able to change stored data upon visiting the profile page.

Consequences or side-effects: 

Storing user information creates a risk of breaching data protection laws.

Be able to assign tickets to tasks
-----------------------------------

Team members who are assigned to a task are able to create tickets 
(see D) for this task.

Tickets can be viewed and worked on by anyone who has access to view the task

Once the ticket is resolved, it should notify the assignees of the task.

Consequences or side-effects: 

Tickets notify the user whenever they are updated, this means that it could 
result in assignees being spammed with notifications.



Be able to create a new account
-------------------------------

In order for an account to be created, the user will have to provide their: 
first name (string), last name (string), email address (string) and password 
(string), all aforementioned fields are mandatory in order for the account to 
be successfully stored in the database.

When the account has been created, the user will be directed to the login page 
where they will then enter the details for their newly created account.

The account can be accessed from all devices with no restrictions assuming the 
same login details are used.

Consequences or side-effects:

The user may not want their data stored due to not knowing what may be done 
with it, resulting in potential law implications.

If unauthorised access were to occur, we would be held responsible, and 
enforcing user security can be costly/difficult.

Storing user data can get expensive despite the cost-friendly options 
Firebase provides.

Be able to access previously created accounts
---------------------------------------------

Once an account is created, the user will be able to login to the account 
upon the launch of the app with their chosen email address (string) 
and password (string).

If a user’s login input matches an email address and password stored in 
the database, the corresponding information stored about that account 
(projects, profile attributes, settings) will be loaded and the user 
will be sent to the profile page.

If an account with the user’s login input is not found in the database, an error 
will be displayed to the user.

Consequences or side-effects:

If a user manages to access an account that is not their own, they may be 
in breach of another user’s privacy.

Storing user login information requires abiding by data protection laws

Be able Manage user settings in Database
----------------------------------------

Once the user has logged in to their accounts they will be able to 
change the settings for their own account.

Once the user enters their preferred settings, this will be correctly 
saved in the database.

When the user logs in on a separate device their preferred settings 
will be correctly applied.

Consequence or side-effect:

Storing complex settings on the servers may be costly.

Users may want to have separate settings on different devices.

