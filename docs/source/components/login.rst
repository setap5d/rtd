Login page
===========
*******
Classes
*******

* MainApp

Returns [MaterialApp] and calls [LoginRegisterScreen] to build first set of widgets.
 inherites the attributes for the StatelessWidget

* LoginRegisterScreen

Creates the state below which allows the user to login into the app if they have an account.#
 inherites the attributes for the StatefulWidget

  * _LoginRegisterScreenState

    attributes
    *showLogin   type boolean

* LoginScreen

Determines whether [LoginScreen] or [RegisterScreen] is displayed.
 Calls [LoginForm] to build widgets that allow for user input.
Inherites the attributes for the StatelessWidget.

* LoginForm

Builds part of the login screen that allows user to enter an email and password and attempt to log in.
Inherites the attributes for the StatelessWidget

* RegisterScreen

Builds the register screen allowing users to enter First name, Last name, email, password and password.
Inherites the attributes for the StatelessWidget

* PasswordResetPage

 Builds the password reset page to allow the user to enter email
Inherites the attributes for the StatelessWidget
