Settings page - settings_page.dart, settings_widgets.dart
=========================================================


settings_page.dart
--------------------

imports
~~~~~~~~

.. code-block:: dart

  import 'package:flutter/material.dart';
  import 'package:cloud_firestore/cloud_firestore.dart';
  import 'settings_widgets.dart';

This first import statement brings in the Material Design library from Flutter. This second import statement imports the Cloud Firestore package for Dart, necessary to access the database. The last import statement bring class that are used for the visual representation of the settings' page.

.. code-block:: dart  

  class SettingsPage extends StatefulWidget {
    const SettingsPage(
        {required this.email,
        required this.settings,
        required this.activeColorScheme,
        super.key});
  
    final String email;
    final Map<String, dynamic> settings;
    final ColorScheme activeColorScheme;
  
    @override
    State<SettingsPage> createState() => _SettingsPageState();
  }

The SettingsPage class is a StatefulWidget in Flutter designed to manage and display user-specific settings. It contains three key attributes: email, which stores the user's email address; settings, a map that holds various user preferences and configurations; and activeColorScheme, which represents the current color scheme of the app. The class also includes a state generation called _SettingsPageState() where the logic to handle changes to the settings is implemented. 


.. code-block:: dart

  class _SettingsPageState extends State<SettingsPage> {
    Future<void> saveSettingsToFireBase(email, settings) async {...)
  
    void checkDisplayMode(email, settings) {...)
  
    void confirmationDialog(String title, String message) {...}
  
    @override
    Widget build(BuildContext context) {
      return Theme(
        data: ThemeData.from(colorScheme: widget.activeColorScheme),
        child: Container(
          color: widget.activeColorScheme.background,
          child: Column(
            children: [
              Expanded(
                child: SingleChildScrollView(
                    child: Container(...),
      );
    }
  }

The _SettingsPageState class serves as the state management component for the SettingsPage widget, handling the user interactions and backend operations associated with settings modifications. This class includes methods to save settings changes to Firebase (saveSettingsToFireBase), check the display mode (checkDisplayMode), and display a confirmation dialog (confirmationDialog).

.. code-block:: dart

    Future<void> saveSettingsToFireBase(email, settings) async {
      FirebaseFirestore db = FirebaseFirestore.instance;
      DocumentReference profileRef = db
          .collection('Profiles')
          .doc('$email')
          .collection('User')
          .doc('Settings');
      await profileRef.set(settings);
    }

The saveSettingsToFireBase method is responsible for saving user settings to the Firebase Firestore database. It takes two parameters: email, which is the user's email address used to identify the user's profile, and settings, a map containing the user's settings data to be saved. Settings are succesfully saved to the database once the method is completed.

.. code-block:: dart

  void checkDisplayMode(email, settings) {
    final displayMode = widget.settings["Display Mode"];
    switch (displayMode) {
      case "Dark Mode":
        saveSettingsToFireBase(email, settings);
        break;
      case "Light Mode":
        saveSettingsToFireBase(email, settings);
        break;
      case "High Contrast Mode":
        saveSettingsToFireBase(email, settings);
        break;
      default:
        confirmationDialog("Error", "Invalid display mode");
    }
  }

The checkDisplayMode method in the _SettingsPageState class is designed to validate and handle changes in the display mode settings of a user's profile. This method takes two parameters: email, which identifies the user's profile, and settings, which is a map containing the user's settings. The method first retrieves the current display mode setting from widget.settings using the key "Display Mode". It then uses a switch statement to check the value of the display mode. If the display mode is set to "Dark Mode", "Light Mode", or "High Contrast Mode", it calls the saveSettingsToFireBase method to update the settings in Firestore. If none of these cases match (indicating an invalid or unrecognized display mode), it triggers a confirmationDialog with an error message. This method does not return a value (void) but directly affects the application's state and user interface by updating settings or informing the user of issues.


.. code-block:: dart

  void confirmationDialog(String title, String message) {
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: Text(title),
          content: Text(message),
          actions: <Widget>[
            TextButton(
              onPressed: () {
                Navigator.of(context).pop();
              },
              child: const Text('Close'),
            ),
          ],
        );
      },
    );
  }


The confirmationDialog method in the _SettingsPageState class is designed to display a simple alert dialog to the user. This method takes two parameters: title and message, which define the text to be displayed in the dialog's title bar and a confirmation changes stating that the colour scheme will be updated once the user logs back in again, respectively.  The method does not return any value (void) as it is solely responsible for displaying information and providing a mechanism to close the dialog. 

.. code-block:: dart

  child: Container(
          color: widget.activeColorScheme.background,
          child: Column(
            children: [
              Expanded(
                child: SingleChildScrollView(
                    child: Container(
                  color: widget.activeColorScheme.background,
                  child: Column(children: <Widget>[
                    Row(children: <Widget>[
                      Expanded(
                          child: Container(
                        color: widget.activeColorScheme.inversePrimary,
                        child: Align(
                          alignment: Alignment.center,
                          child: Text("Settings",
                              style: TextStyle(
                                  fontSize: 36,
                                  color: widget.activeColorScheme.onBackground,
                                  backgroundColor:
                                      widget.activeColorScheme.inversePrimary)),
                        ),
                      ))
                    ]),
                    Container(
                        padding: const EdgeInsets.all(16.0),
                        child: Column(
                            crossAxisAlignment: CrossAxisAlignment.start,
                            children: <Widget>[
                              Text(
                                "Display",
                                style: TextStyle(
                                    fontWeight: FontWeight.w600,
                                    fontSize: 26,
                                    color: widget.activeColorScheme.onBackground),
                              ),
                              RadioSetting(
                                  settingName: "Display Mode",
                                  optionsList: const [
                                    "Light Mode",
                                    "Dark Mode",
                                    "High Contrast Mode",
                                  ],
                                  defaultOption: widget.settings['Display Mode'],
                                  onChanged: (selectedOption) {
                                    setState(() {
                                      widget.settings['Display Mode'] =
                                          selectedOption;
                                    });
                                  },
                                  colorScheme: widget.activeColorScheme),
                            ]))
                  ]),
                )),
              ),
              ElevatedButton(
                style: ButtonStyle(
                    backgroundColor: MaterialStateProperty.all<Color>(
                  Theme.of(context).colorScheme.inversePrimary,
                )),
                onPressed: () {
                  saveSettingsToFireBase(widget.email, widget.settings);
                  confirmationDialog("Changes Saved",
                      "Please restart application to apply changes");
                },
                child: Text(
                  'Save Changes',
                  style: TextStyle(color: widget.activeColorScheme.onBackground),
                ),
              ),
            ],
          ),
        ),
The child widget of the _SettingsPageState class defines the layout and content of the settings page within the app. It consists of a Container widget with a background color set to widget.activeColorScheme.background, ensuring consistency with the app's overall color scheme. 


settings_widgets.dart
----------------------

imports
~~~~~~~~
.. code-block:: dart

  import 'package:flutter/material.dart';

This first import statement brings in the Material Design library from Flutter.

.. code-block:: dart

  class RadioSetting extends StatefulWidget {
    final String settingName;
    final List<String> optionsList;
    final String defaultOption;
    final Function(String) onChanged;
    final ColorScheme colorScheme;
  
    const RadioSetting(
        {super.key,
        required this.settingName,
        required this.optionsList,
        required this.defaultOption,
        required this.onChanged,
        required this.colorScheme});
  
    @override
    State<RadioSetting> createState() => _RadioSettingState();
  }

The RadioSetting class is a StatefulWidget in Flutter designed specifically to handle the selection of options for the SettingsPage class using radio buttons.  It has several key attributes: settingName for the setting's label, optionsList containing the available choices, defaultOption indicating the initially selected option, onChanged as a callback for handling changes, and colorScheme for styling. The createState() method links this configuration with the _RadioSettingState class, which manages the state of the selected radio button and ensures the widget reflects the current selection.

.. code-block:: dart

  class _RadioSettingState extends State<RadioSetting> {
    String selectedOption = "";
  
    _RadioSettingState();

    void initState() {...}
  
    @override
    Widget build(BuildContext context) {
      return Theme(
        data: ThemeData.from(colorScheme: widget.colorScheme),
        child: Container(
          color: widget.colorScheme.background,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: <Widget>[
              Text(
                widget.settingName,
                style: const TextStyle(fontWeight: FontWeight.w600, fontSize: 20),
              ),
              Column(
                children: widget.optionsList.map((option) {
                  return ListTile(
                    title: Text(option, style: const TextStyle(fontSize: 18)),
                    leading: Radio<String>(
                      value: option,
                      groupValue: selectedOption,
                      onChanged: (value) {
                        setState(() {
                          selectedOption = value as String;
                          widget.onChanged(selectedOption);
                        });
                      },
                    ),
                  );
                }).toList(),
              ),
            ],
          ),
        ),
      );
    }
  }


The _RadioSettingState class manages the state for the RadioSetting widget, primarily handling user interactions with radio button options. This class starts with an attribute selectedOption, initialized as an empty string to track the currently selected radio button. The build method constructs the UI which includes a Column displaying the setting's name and the list of radio buttons generated dynamically from widget.optionsList. Each radio button, when selected, triggers an update to selectedOption using setState, ensuring the UI reflects the current selection and also invokes the widget.onChanged callback to notify of changes. 

.. code-block:: dart

    void initState() {
      super.initState();
      if (widget.defaultOption == "Light Mode") {
        selectedOption = widget.optionsList[0];
      } else if (widget.defaultOption == "Dark Mode") {
        selectedOption = widget.optionsList[1];
      } else if (widget.defaultOption == "High Contrast Mode") {
        selectedOption = widget.optionsList[2];
      } else if (widget.defaultOption == "Colour Blind Mode") {
        selectedOption = widget.optionsList[3];
      }
    }


The initState method in the _RadioSettingState class is overridden to set up the initial state of the radio buttons based on the defaultOption specified in the RadioSetting widget. This method does not take any parameters nor does it return any value, as it's designed to execute when the state is first created, setting internal state variables before the widget builds. Specifically, it checks the defaultOption provided and sets the selectedOption state variable to the corresponding value from the optionsList. 







