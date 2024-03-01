Welcome to Project Master's documentation!
===================================

**Project Master** is a Flutter application for managing team projects

It allows users to setup a series of *projects* and *tasks* in an organised manner.

User Requirments
=================

1. Be able to manage multiple projects at once 

2. Be able to manage multiple tasks within projects 

3. Be able to store name, email, phone number(optional), picture(optional), average contribution score for past projects, skills (programming languages used)

6. Be able to set a deadline reminder time (hour, day, etc.)

7. Be able to send notifications straight to the phone 

8. Be able to visualise progress through a completion/progress bar (colours vary depending on individual)

9. Allow different permissions for different team members

10. Be able to upload files onto the platform 

11. Be able to assign tickets to tasks 

*4. Be able to assign different roles such as admin that has the ability to control the group and access team member tasks and view their progress 

*5. Allow for automated task assignment with ability to manually edit task assignment

Elements of implementation
=======================
1. App was programmed in Flutter

2. Flutter run entered into the terminal runs the app

3. Github was used to keep track of the changes


Instructions on how to set up/run the project
=================

- You need to have a Flutter SDK version above 3.2.6 and Dart 3.3.0 installed as it's a prerequisite for Flutter.
- The only Firebase components that need to be installed are Firebase Core (firebase_core) and Firebase Authentication (firebase_auth).
- *Flutter Chart (fl_chart 0.66.2) is necessary in order to be able to visualise the "contribution score" feature of our app.


Check out the :doc:`usage` section for further information, including
how to :ref:`installation` the project.

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   usage

Discussion of the main components
----------------------------------
GUI

Main

Login Manager

Local Storage

Tickets Manager

Settings Manager

Notification Manager

File Manager

Permission Manager

Profile Manager

Projects Manager
