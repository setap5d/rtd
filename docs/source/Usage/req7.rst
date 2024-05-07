Requirement 5: Be able to manage user settings in Database
=========================================================

Once the user has logged in to their accounts they will be able to change the settings for their own account.
--------------------------------------------------------------------------------------------------------------

The settings screen can be accessed by through the sidebar at any time.

.. image:: ../images/req7/sec1/image1.png
    :width: 600px
    :align: center
    :alt: Image of the settings screen

The settings shown in the image above are the default settings that the user will see when they first log in. 
These can be edited by the user to their own preference.

Once the user enters their preferred settings, this will be correctly saved in the database.
--------------------------------------------------------------------------------------------

After the user decides which settings match their preference, they can click the save button to save their settings.

.. image:: ../images/req7/sec2/image1.png
    :width: 600px
    :align: center
    :alt: Image of the settings screen with edited settings

The application must be then be restarted to apply the new settings.

When the user logs in on a separate device their preferred settings will be correctly applied.
----------------------------------------------------------------------------------------------

Once the user has chosen their desired settings, these will be saved in the database. 
When the user logs in to their account on any device, their preferred settings will be read from the database and applied.

Consequence or side-effect:
---------------------------

- Storing complex settings on the servers may be costly.
- Users may want to have separate settings on different devices.
