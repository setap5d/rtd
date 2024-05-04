Login page - main.dart
======================

main
-----

.. code-block:: dart

    Future<void> main() async {
        WidgetsFlutterBinding.ensureInitialized();
        await Firebase.initializeApp(
            options: DefaultFirebaseOptions.web,
        );
        runApp(const MainApp());
    }

In the main function, we initialize Firebase and create an instance of MainApp to begin running the app.


MainApp
-------

.. code-block:: dart

    class MainApp extends StatelessWidget {
        const MainApp({Key? key}) : super(key: key); // The key is used to uniquely identify the instance of the widget

        @override
        Widget build(BuildContext context) {
            return const MaterialApp(
                home: LoginRegisterScreen(),
                debugShowCheckedModeBanner: false,
            );
        }
    }

MainApp is a stateless widget that returns a MaterialApp widget with the LoginRegisterScreen as the home page.

LoginRegisterScreen
-------------------

.. code-block:: dart

    class LoginRegisterScreen extends StatefulWidget {
        const LoginRegisterScreen({Key? key}) : super(key: key);

        @override
        _LoginRegisterScreenState createState() => _LoginRegisterScreenState();
    }

This class is a stateful widget that returns an instance of _LoginRegisterScreenState.
This class is required as the login and register forms require state as it needs to update the UI based on the user's actions.

_LoginRegisterScreenState
-------------------------

.. code-block:: dart

    class _LoginRegisterScreenState extends State<LoginRegisterScreen> {
        final bool showLogin = true;
        @override
        Widget build(BuildContext context) {
            return Scaffold(
                body: Center(
                    
                    ...

                    child: showLogin ? const LoginScreen() : const RegisterScreen(),
                    
                    ...
                ),
            );

        }
    }

This class takes in the state from LoginRegisterScreen and returns a Scaffold widget with a child that is either a LoginScreen or a RegisterScreen based on the value of showLogin.

LoginScreen
------------

.. code-block:: dart

    class LoginScreen extends StatelessWidget {
        const LoginScreen({Key? key}) : super(key: key);

        @override
        Widget build(BuildContext context) {
            void switchToRegister() {
                Navigator.push(
                    context,
                    MaterialPageRoute(builder: (context) => const RegisterScreen()),
                );
            }

This class begins by building the switchToRegister method which is used to call the RegisterScreen class when the user clicks on the register button.

..  code-block:: dart

    GestureDetector(
        // Allows user to call [switchToRegister]
        onTap: (switchToRegister),
        child: const Text(
            'Register Here',
            style: TextStyle(fontSize: 20, color: Colors.blue),
        ),
    ),

This GestureDetector widget is used to call the switchToRegister method when the user taps on the 'Register Here' text.


LoginForm
---------

The LoginForm class is a stateless widget that returns a form with text fields that require user input.

.. code-block:: dart

    // Gets profile information for the profile page, needs to be initialised on build/before screen move
    Future getProfileInfo(email) async {
        FirebaseFirestore db = FirebaseFirestore.instance;
        List<dynamic> profDetails = [];
        var profRef = await db.collection('Profiles').doc(email).get();
        profDetails.add(profRef.get('First Name'));
        profDetails.add(profRef.get('Last Name'));
        profDetails.add(email);
        profDetails.add(profRef.get('Phone Number'));
        profDetails.add(profRef.get('Skills'));
        return profDetails;
    }

The getProfileInfo method is used to retrieve the user's profile information from the Firestore database. It takes in the user's email as a parameter and returns a list of the user's profile details.
This is used throughout the app for a variety of checks, and to display the user's profile information on the profile page.

.. code-block:: dart

    Future getSettingsFromFireBase(email) async {
        FirebaseFirestore db = FirebaseFirestore.instance;
        Map<String, dynamic> data = {};
        await db
            .collection('Profiles')
            .doc('$email')
            .collection('User')
            .doc('Settings')
            .snapshots()
            .listen((snapshot) async {
        data = snapshot.data() as Map<String, dynamic>;
        });
        await Future.delayed(const Duration(milliseconds: 100));
        return Future.value(data);
    }

This method uses the email parameter to retrieve the user's settings from the Firestore database.
It returns a map of the user's settings.


.. code-block:: dart

    @override
    Widget build(BuildContext context) {
        final TextEditingController emailController = TextEditingController();
        final TextEditingController passwordController = TextEditingController();
        List<dynamic> projectIDs = [];
        List<Project> projects = [];
        List<dynamic> profDetails = [];
        Map<String, dynamic> settings = {};

The build method begins by initializing the emailController and passwordController text editing controllers.
The controllers store the user input for the login and are attached to TextFormField widgets.
The other values are retrieved from Firebase once the user passes the log in checks.

.. code-block:: dart

    void switchToPasswordReset() {
        Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => const PasswordResetPage()),
        );
    }

The switchToPasswordReset method is used to call the PasswordResetPage class when the user clicks on the 'Forgot Password?' button.



Login Details Check - checkLoginDetails()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: dart

    void checkLoginDetails() async {
      final profileSnapshot = await FirebaseFirestore.instance
          .collection('Profiles')
          .doc(emailController.text)
          .get();


This method is used to check the user's login details against the Firestore database.
It begins by retrieving the user's profile snapshot from the Firestore database using the emailController text as the document ID.

.. code-block:: dart

      if (profileSnapshot.exists) {
        if (profileSnapshot.get('Password') == passwordController.text) {
          projectIDs = profileSnapshot.get('Project IDs');
          projects = [];
          for (int i = 0; i < projectIDs.length; i++) {
            Project newProject = Project();
            final projectSnapshot = await FirebaseFirestore.instance
                .collection('Projects')
                .doc(projectIDs[i])
                .get();
            newProject.projectName = projectSnapshot.get('Title');
            newProject.deadline = projectSnapshot.get('Deadline');
            newProject.leader = projectSnapshot.get('Project Leader');
            projects.add(newProject);
          }
          profDetails = await getProfileInfo(emailController.text);
          settings = await getSettingsFromFireBase(emailController.text);
          Navigator.push(
            context,
            MaterialPageRoute(
              builder: (context) => NavigationPage(
                email: emailController.text,
                projectIDs: projectIDs,
                projects: projects,
                profDetails: profDetails,
                settings: settings,
                selectedIndex: 1,
              ),
            ),
          );
        } 
        
If the user's profile exists in the database and the password matches the password stored in the database, 
the user's project IDs and projects along with the attributes are retrieved from the database.

If the email does not exist in the database, or the password does not match the password stored in the database, an error is returned to the user.

RegisterScreen
--------------

The RegisterScreen class is a stateless widget that returns a form with text fields that require user input.
It is used to register a new user to the database.

.. code-block:: dart

    class RegisterScreen extends StatelessWidget {
    const RegisterScreen({Key? key}) : super(key: key);

    @override
    Widget build(BuildContext context) {
        final TextEditingController firstNameController = TextEditingController();
        final TextEditingController lastNameController = TextEditingController();
        final TextEditingController emailController = TextEditingController();
        final TextEditingController passwordController = TextEditingController();
        final List<String> placeholder = [];

The build method begins by initializing the firstNameController, lastNameController, emailController, and passwordController text editing controllers. 
These will take inputs from the user and are attached to TextFormField widgets, they define the user's account details.
The placeholder list is used to store the user's projects.


.. code-block:: dart

    void registerNewAccount() async {
      try {
        final fullName =
            '${firstNameController.text} ${lastNameController.text}';

        final fullNameSnapshot = await FirebaseFirestore.instance
            .collection('Profiles')
            .where('Full Name', isEqualTo: fullName)
            .get();

        if (fullNameSnapshot.docs.isNotEmpty) {
          showDialog(
            context: context,
            builder: (context) {
              return AlertDialog(
                title: const Text('Error'),
                content: const Text('This name is already registered.'),
                actions: [
                  TextButton(
                    onPressed: () {
                      Navigator.of(context).pop();
                    },
                    child: const Text('OK'),
                  ),
                ],
              );
            },
          );
          return;
        }

The registerNewAccount method is used to register a new user to the Firestore database.
It begins by creating a fullName variable that takes the firstName and lastName inputs and concatenates them.
This variable is then checked to see if it already exists in the database, if so it returns an alert error. 

.. code-block:: dart

        final emailSnapshot = await FirebaseFirestore.instance
            .collection('Profiles')
            .doc(emailController.text)
            .get();

        if (emailSnapshot.exists) {
          showDialog(
            context: context,
            builder: (context) {
              return AlertDialog(
                title: const Text('Error'),
                content: const Text('Email is already in use.'),
                actions: [
                  TextButton(
                    onPressed: () {
                      Navigator.of(context).pop();
                    },
                    child: const Text('OK'),
                  ),
                ],
              );
            },
          );
          return;
        }

Next, the email input is checked to see if it already exists and also returns an alert error if it does.

.. code-block:: dart

    await FirebaseAuth.instance.createUserWithEmailAndPassword(
        email: emailController.text,
        password: passwordController.text,
    );

    await FirebaseFirestore.instance
        .collection('Profiles')
        .doc(emailController.text)
        .set({
    'First Name': firstNameController.text,
    'Last Name': lastNameController.text,
    'Password': passwordController.text,
    'Phone Number': '',
    'Skills': '',
    'Project IDs': placeholder
    });
    await FirebaseFirestore.instance
        .collection('Profiles')
        .doc(emailController.text)
        .collection('User')
        .doc('Settings')
        .set({
    'Display Mode': 'Light Mode',
    'Project Deadline Notifications': true,
    'Task Deadline Notifications': true,
    'Ticket Notifications': true
    });
    await FirebaseFirestore.instance
        .collection('Profiles')
        .doc(emailController.text)
        .collection('User')
        .doc('ProfilePic')
        .set({"Download URL": null});

    Navigator.push(
          context,
          MaterialPageRoute(builder: (context) => const LoginScreen()),
        );
      } catch (e) {}
    }

Here, after all checks have passed, the user is registered.
This means that the user's inputs are stored in the Firestore database under the 'Profiles' collection.
Once registration is successful, the user is navigated back to the LoginScreen so they can login with their account details.

.. code-block:: dart

    void switchToLoginScreen() {
      Navigator.pop(context);
    }

This method is used to navigate back to the LoginScreen from the register page.

.. code-block:: dart

    return Scaffold(
        body: SingleChildScrollView(

            ...

            child: TextFormField(
                controller: firstNameController,
                decoration: const InputDecoration(
                    labelText: 'First Name',
                    prefixIcon: Icon(Icons.person),
                ),
            ),

            ...

            child: TextFormField(
                controller: lastNameController,
                decoration: const InputDecoration(
                    labelText: 'Last Name',
                    prefixIcon: Icon(Icons.person),
                ),
            ),

            ...

            TextFormField(
                  controller: emailController,
                  decoration: const InputDecoration(
                    labelText: 'Email',
                    prefixIcon: Icon(Icons.email),
                    ),
            ),

            ...

            TextFormField(
                controller: passwordController,
                decoration: const InputDecoration(
                    labelText: 'Password',
                    prefixIcon: Icon(Icons.lock),
                ),
                obscureText: true,
            ),

            ...

            TextFormField(
                decoration: const InputDecoration(
                    labelText: 'Confirm Password',
                    prefixIcon: Icon(Icons.lock),
                ),
                obscureText: true,
            ),

            ...

This part of the scaffold is used to create the inputs for the register form. It requires the first name, last name,
email, password, and confirm password fields to be filled out by the user.

.. code-block:: dart

    child: ElevatedButton(
        onPressed: (registerNewAccount),
        child: const Text(
        'Register',

        ...
    
A 'Register' button is provided for the user to submit their details and register their account.
This button calls the registerNewAccount method when pressed.

.. code-block:: dart

    Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
        const Text(
            'Already have an account? ',
            style: TextStyle(
            fontSize: 20,
            ),
        ),
        GestureDetector(
            onTap: (switchToLoginScreen),
            child: const Text(
            'Login Here',
            style: TextStyle(fontSize: 20, color: Colors.blue),
            ),
        ),
        ],
    ),

At the bottom of the register form, a button text is provided for users who already have an account
so that they can return to the login screen.

PasswordResetPage
-----------------