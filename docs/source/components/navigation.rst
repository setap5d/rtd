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

.. code-block:: dart

    class _NavigationPageState extends State<NavigationPage> {
        late ColorScheme activeColorScheme = updateColorScheme();

        _NavigationPageState(
            {required this.selectedIndex});

        int selectedIndex;
        
        ColorScheme updateColorScheme() {
            final displayMode = widget.settings["Display Mode"];
            switch (displayMode) {
                case "Dark Mode":
                    return AppColorSchemes.darkMode;
                case "Light Mode":
                    return AppColorSchemes.lightMode;
                case "High Contrast Mode":
                    return AppColorSchemes.highContrastMode;
                default:
                    return AppColorSchemes.lightMode; // or any default color scheme
            }
        }

        bool isExtended() {
            if (MediaQuery.of(context).size.width >= 800) {
            return true;
            } else {
            return false;
            }
        }

The state for the navigation bar is defined here. This is also where the color scheme is updated
with the updateColorScheme function. This checks the settings page for the display mode and sets the
color scheme of the navigation bar accordingly. The isExtended function checks the width of the screen
to determine whether the navigation bar should be extended or not - deciding whether it shows the 
full name of pages or just icons.


.. code-block:: dart

    @override
    Widget build(BuildContext context) {
        final List<Widget> pages = [
            ProfilePage(email: widget.email, profDetails: widget.profDetails),
            ProjectsPage(
                title: 'My Projects',
                email: widget.email,
                projectIDs: widget.projectIDs,
                projects: widget.projects,
                settings: widget.settings,
                profDetails: widget.profDetails,
                activeColorScheme: activeColorScheme,
            ),
            SettingsPage(
                email: widget.email,
                settings: widget.settings,
                activeColorScheme: activeColorScheme,
            ),
        ];

In the build function, the pages are defined. These are the pages that the navigation bar will navigate to.
Each of these pages are given unique attributes that are passed to them when they are navigated to.

.. code-block:: dart

    void changePage(index) {
      setState(() {
        if (index == pages.length) {
          Navigator.of(context).pop();
        } else {
          selectedIndex = index;
        }
      });
    }

The changePage function is used to change the page that is currently being displayed.
This is done by changing the selectedIndex attribute of the navigation bar.

.. code-block:: dart

    return Scaffold(
      body: Theme(
        data: ThemeData.from(colorScheme: activeColorScheme),
        child: Row(
          children: [
            SafeArea(
              child: NavigationRail(
                selectedIndex: selectedIndex,
                onDestinationSelected: (changePage),
                extended: isExtended(),
                groupAlignment: -1.0,
                destinations: const [
                  NavigationRailDestination(
                    icon: Icon(Icons.account_circle),
                    label: Text('Profile'),
                  ),
                  NavigationRailDestination(
                    icon: Icon(Icons.home),
                    label: Text('Home'),
                  ),
                  NavigationRailDestination(
                    icon: Icon(Icons.settings),
                    label: Text('Settings'),
                  ),
                  NavigationRailDestination(
                    icon: Icon(Icons.logout),
                    label: Text('Logout'),
                  ),
                ],
              ),
            ),
            Expanded(child: pages[selectedIndex]),
          ],
        ),
      ),
    );

Finally, the scaffold is returned. This scaffold contains the navigation rail and the icons that the navigation bar 
use to point to the different pages. Once one of the icons is clicked, the changePage function is called with the
index of the icon that was clicked.