Navigation bar - navigation.dart
=================================

imports
-------

.. code-block:: dart

    import 'package:flutter/material.dart';
    import 'projects_page.dart';
    import 'profile.dart';
    import 'project_format.dart';
    import 'settings_page.dart';

The navigation bar is used throughout the app to navigate between the different pages. 
Therefore it requires imports from the profile, settings, and profile pages.

AppColorSchemes
---------------

.. code-block:: dart

    class AppColorSchemes {
        static final lightMode = ColorScheme.fromSeed(
            seedColor: Colors.blue, brightness: Brightness.light);

        static final darkMode =
            ColorScheme.fromSeed(seedColor: Colors.blue, brightness: Brightness.dark);

    // Taken from offical Flutter website
        static final highContrastMode = ColorScheme.fromSeed(
            seedColor: const Color(0xffefb7ff),
            brightness: Brightness.dark,
        ).copyWith(
            primaryContainer: const Color(0xffefb7ff),
            onPrimaryContainer: Colors.black,
            secondaryContainer: const Color(0xff66fff9),
            onSecondaryContainer: Colors.black,
            error: const Color(0xff9b374d),
            onError: Colors.white,
        );
    }

This class contains static variables that apply the colour scheme to the widgets. 
This color scheme can be edited in the settings page. The reason this is set here is because
the navigation bar is used throughout the app and therefore requires a color scheme, and the colour
scheme is something that should be kept consistent between different login sessions unless edited.

NavigationPage
--------------

.. code-block:: dart

    class NavigationPage extends StatefulWidget {
        const NavigationPage(
            {Key? key,
            required this.email,
            required this.projectIDs,
            required this.projects,
            required this.profDetails,
            required this.settings,
            required this.selectedIndex})
            : super(key: key);

        final String email;
        final List<dynamic> projectIDs;
        final List<Project> projects;
        final List<dynamic> profDetails;
        final Map<String, dynamic> settings;
        final int selectedIndex;

        @override
        State<NavigationPage> createState() => _NavigationPageState(
            selectedIndex: selectedIndex);
    }

This class is the main class for the navigation bar. 
It is a stateful widget that defines the attributes and parameters 
that are passed to the navigation bar.