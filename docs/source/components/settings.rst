Settings page - settings_page.dart, settings_widgets.dart
=========================================================


settings_page.dart
--------------------

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
      );
    }
  }

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


settings_widgets.dart
----------------------

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


.. code-block:: dart

  class _RadioSettingState extends State<RadioSetting> {
    String selectedOption = "";
  
    _RadioSettingState();
  
  
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







