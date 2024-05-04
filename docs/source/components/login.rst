Login page - main.dart
======================

MainApp
-------

.. code-block:: dart

    Future<void> main() async {
        WidgetsFlutterBinding.ensureInitialized();
        await Firebase.initializeApp(
            options: DefaultFirebaseOptions.web,
        );
        runApp(const MainApp());
    }

In the main function, we initialize Firebase and create an instance of MainApp to begin running the app.