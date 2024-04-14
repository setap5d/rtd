Be able to create a new account
===============================

In order for an account to be created, the user will have to provide their: first name (string), last name (string), email address (string) and password (string), all aforementioned fields are mandatory in order for the account to be successfully stored in the database.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

When first launching the application, the user will be greeted with the login page, where they will have the option to create a new account.

.. image:: ../images/req5/sec1/image1.png
    :width: 600px
    :align: center
    :alt: Image of the login screen

Upon clicking the "Create Account" button, the user will be directed to the account creation page where they will be prompted to enter their 
first name, last name, email address and password. 

.. image:: ../images/req5/sec1/image2.png
    :width: 600px
    :align: center
    :alt: Image of the account creation screen

Once all fields have been filled out, the user will then click the "Create Account" button to submit the information. 

.. image:: ../images/req5/sec1/image3.png
    :width: 600px
    :align: center
    :alt: Image of the account creation screen with all fields filled out

When the account has been created, the user will be directed to the login page where they will then enter the details for their newly created account.
------------------------------------------------------------------------------------------------------------------------------------------------------

If their inputs are valid, the account will be created and the user will be directed to the login page 
where they will then enter the details for their newly created account in order to login.

Otherwise, the user will remain on the account creation screen until all fields are filled out correctly and submitted.

The account can be accessed from all devices with no restrictions assuming the same login details are used.
-----------------------------------------------------------------------------------------------------------

After an account has been created, it will be stored in the cloud database (Firebase) and can be accessed from any device with an internet connection.

Consequences or side-effects:
-----------------------------

- The user may not want their data stored due to not knowing what may be done with it, resulting in potential law implications.

- If unauthorised access were to occur, we would be held responsible, and enforcing user security can be costly/difficult.

- Storing user data can get expensive despite the cost-friendly options Firebase provides.