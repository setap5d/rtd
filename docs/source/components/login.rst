Login page - main.dart
======================

MainApp
-------

.. code-block:: dart
    :emphasize-lines: 12,13,14,15,16,17,18

    Future<void> main() async {
        WidgetsFlutterBinding.ensureInitialized();
        await Firebase.initializeApp(
            options: DefaultFirebaseOptions.web,
        );
        runApp(const MainApp());
    }

In the main function, we initialize Firebase and create an instance of MainApp to begin running the app.