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
The controllers store the user input for the login.
The other values are retrieved from Firebase once the user passes the log in checks.

