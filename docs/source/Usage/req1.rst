Be able to manage multiple projects at once
===========================================

Users will be able to create new projects (see A).
--------------------------------------------------

.. image:: ../images/req1/sec1/image.png
    :width: 600px
    :align: center
    :alt: Image of the project  page

Initially, the project page will have no projects, the user can click the "+" button to create a new project. 
This will open a modal where the user can enter the project title, deadline, and team leader. 

.. image:: ../images/req1/sec1/image2.png
    :width: 600px
    :align: center
    :alt: Image of the modal to create a new project

Once the user enters the desired details of the project, they can click the "Create" button to create the project. The project will then be displayed on the project page.

.. image:: ../images/req1/sec1/image3.png
    :width: 600px
    :align: center
    :alt: Image of the project page with a new project

Users will be able to view multiple assigned projects at any one time.
-----------------------------------------------------------------------

.. image:: ../images/req1/sec2/image.png
    :width: 600px
    :align: center
    :alt: Image of the project page with multiple projects

As the user creates more projects, they will be displayed on the project page.

Users will be able to change the name and deadline of a project.
----------------------------------------------------------------

.. image:: ../images/req1/sec3/image.png
    :width: 600px
    :align: center
    :alt: Image of the project page with the edit project modal

Each project has a dropdown menu that can be accessed by clicking the three dots on the right side of the project widget.
This will open a dropdown menu with the options to edit the project.
Selecting the "Edit" option will open a modal where the user can change the project title and deadline.

.. image:: ../images/req1/sec3/image2.png
    :width: 600px
    :align: center
    :alt: Image of the modal to edit a project

.. image:: ../images/req1/sec3/image3.png
    :width: 600px
    :align: center
    :alt: Image of the modal with the updated project details

Once the user has made the desired changes, they can click the "Save" button to save the changes.

.. image:: ../images/req1/sec3/image4.png
    :width: 600px
    :align: center
    :alt: Image of the project page with the updated project

This will update the project widget on the project page and the database with the new details.

Users will be able to delete projects.
---------------------------------------

.. image:: ../images/req1/sec4/image.png
    :width: 600px
    :align: center
    :alt: Image of the project page with the dropdown menu open

.. image:: ../images/req1/sec4/image2.png
    :width: 600px
    :align: center
    :alt: Image of the project page with the delete project modal

Once the delete option is selected, a modal will open asking the user to confirm the deletion of the project.

.. image:: ../images/req1/sec4/image3.png
    :width: 600px
    :align: center
    :alt: Image of the project page with the project deleted

Selecting the "Yes" option will delete the project from the project page and database.

Projects will have a title (string), deadline (date), description (string), and list of team members (see C) (list).
--------------------------------------------------------------------------------------------------------------------

This can be seen above when creating the project, the user can enter the project title, deadline, and team leader and edit these at any time.

Consequences or side-effects: 
-----------------------------

- If a user has too many projects, it could cause the application to slow down on the projects page due to the number of widgets being displayed to them.

- Storing more projects on our servers would cost more money as we store more data.