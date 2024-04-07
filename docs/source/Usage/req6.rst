Be able to access previously created accounts
=============================================

Once an account is created, the user will be able to login to the account upon the launch of the app with their chosen email address (string) and password (string).
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

If a user’s login input matches an email address and password stored in the database, the corresponding information stored about that account (projects, profile attributes, settings) will be loaded and the user will be sent to the profile page.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If an account with the user’s login input is not found in the database, an error will be displayed to the user.
---------------------------------------------------------------------------------------------------------------

Consequences or side-effects:
-----------------------------

- If a user manages to access an account that is not their own, they may be in breach of another user’s privacy.

- Storing user login information requires abiding by data protection laws.
