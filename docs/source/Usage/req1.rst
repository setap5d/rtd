Be able to manage multiple projects at once
===========================================

Users will be able to create new projects (see A).
--------------------------------------------------

.. image:: ../images/req1/sec1/image.png
    :width: 600px
    :align: center
    :alt: Image of the project creation page

Initially, the project page will have no projects, the user can click the "+" button to create a new project. 
This will open a modal where the user can enter the project title, deadline, and team leader. 

.. image:: ./images/req1/sec1/image2.png
    :width: 600px
    :align: center
    :alt: Image of the modal to create a new project

Once the user enters the desired details of the project, they can click the "Create" button to create the project. The project will then be displayed on the project page.

.. image:: ./images/req1/sec1/image3.png
    :width: 600px
    :align: center
    :alt: Image of the project page with a new project

Users will be able to view multiple assigned projects at any one time.
-----------------------------------------------------------------------

Users will be able to change the name and deadline of a project.
----------------------------------------------------------------

Users will be able to delete projects.
---------------------------------------

Projects will have a title (string), deadline (date), description (string), and list of team members (see C) (list).
--------------------------------------------------------------------------------------------------------------------

Consequences or side-effects: 
-----------------------------

- If a user has too many projects, it could cause the application to slow down on the projects page due to the number of widgets being displayed to them.

- Storing more projects on our servers would cost more money as we store more data.