Project page - project_format.dart, project_tiles.dart, projects_page.dart, shared.dart
=======================================================================================


project_format.dart
--------------------


.. code-block:: dart

  class Project {
    String projectName = 'Project Name';
    String deadline = 'Deadline for Project';
    String leader = 'Leader: ';
    bool archived;
    bool isExpanded;
  
    Project({this.archived = false, this.isExpanded = false});
  }

This class is used in the projects_page.dart file to create Project. The class includes string properties for the project's name, deadline, and leader's name, as well as boolean properties to indicate whether the project is archived (future feature) and whether its details are shown in an expanded view. The constructor uses named parameters with default values, allowing these properties to be optionally specified at the time of instantiation.


project_tiles.dart
--------------------
imports
~~~~~~~~

.. code-block:: dart

  import 'package:flutter/material.dart';
  import 'project_format.dart';
  import 'shared.dart';
  import 'package:cloud_firestore/cloud_firestore.dart';

The flutter material import is required for the Flutter framework. The firebase import is required for the Firestore database. The project_format.dart file is imported to format the project details. The shared.dart imports two functions that are used by the file.

.. code-block:: dart

  class ProjectTile extends StatefulWidget {
    final Project project;
    final VoidCallback onDelete;
    final List<dynamic> projectIDs;
    final int projectIndex;
    final String email;
  
    const ProjectTile({
      Key? key,
      required this.project,
      required this.onDelete,
      required this.projectIDs,
      required this.projectIndex,
      required this.email,
    }) : super(key: key);
  
    @override
    _ProjectTileState createState() => _ProjectTileState();
  }

The ProjectTile class is to inititialise the frontend of a project's information. It is used to initialize the frontend representation of a project's information. It incorporates an instance of the Project class, which manages the project's backend data. Alongside, it handles a deletion functionality through the onDelete callback, a list of project IDs the user is part of (projectIDs), and utilizes a specific project index (projectIndex) to differentiate between projects. The email parameter is used to show that the user is part of the created project. This override of createState() returns an instance of _ProjectTileState, which actually display the project.

.. code-block:: dart

  class _ProjectTileState extends State<ProjectTile> {
  
    Future<void> showAddAssigneesDialog(BuildContext context) async  {...}
    void showErrorDialog(String message) {...}
    Future<void> showEditDialog(BuildContext context) async {...}
    @override
    Widget build(BuildContext context) {
      return Container(
        decoration: BoxDecoration(
          color: Theme.of(context).colorScheme.inversePrimary,
          borderRadius: BorderRadius.circular(10.0),
        ),
        child: Stack(
          children: [
            Column(
              crossAxisAlignment: CrossAxisAlignment.center,
              children: [
                Expanded(
                  child: Center(
                    child: Text(
                      'Project Name: ${widget.project.projectName}',
                      style: const TextStyle(fontWeight: FontWeight.bold),
                      textAlign: TextAlign.center,
                    ),
                  ),
                ),
                Expanded(
                  child: Center(
                    child: Text(
                      'Deadline: ${widget.project.deadline}',
                      textAlign: TextAlign.center,
                    ),
                  ),
                ),
                Expanded(
                  child: Center(
                    child: Text(
                      widget.project.leader,
                      textAlign: TextAlign.center,
                    ),
                  ),
                ),
              ],
            ),
            Align(...)
          ],
        ),
      );
    }

The _ProjectTileState is a state of the ProjectTile class which display the project to the screen. It has the showAddAssigneesDialog and showEditDialog used by the Align widget, while the showErrorDialog is used by the 2 methods mentioned.


.. code-block:: dart

  Future<void> showAddAssigneesDialog(BuildContext context) async {
    TextEditingController emailController = TextEditingController();

    return showDialog<void>(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Add Assignees'),
          content: SingleChildScrollView(
            child: Column(
              children: <Widget>[
                TextField(
                  controller: emailController,
                  decoration: const InputDecoration(labelText: 'Email'),
                ),
              ],
            ),
          ),
          actions: <Widget>[
            TextButton(
              child: const Text('Add'),
              onPressed: () async {
                String enteredEmail = emailController.text.trim();
                if (enteredEmail.isEmpty) {
                  showErrorDialog('Please enter an email.');
                  return;
                }

                bool alreadyAssignee = false;
                final otherUserRef = await FirebaseFirestore.instance
                    .collection('Profiles')
                    .doc(enteredEmail)
                    .get();
                if (!otherUserRef.exists) {
                  showDialog(
                    context: context,
                    builder: (context) {
                      return AlertDialog(
                        title: const Text('Error'),
                        content: const Text("Email doesn't exist"),
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
                } else {
                  // Email exists, proceed with adding assignee
                  List<dynamic> emailProjectIDs =
                      otherUserRef.get('Project IDs');
                  for (int i = 0; i < emailProjectIDs.length; i++) {
                    if (emailProjectIDs[i] ==
                        widget.projectIDs[widget.projectIndex]) {
                      alreadyAssignee = true;
                      break;
                    }
                  }
                  if (!alreadyAssignee) {
                    // Add the project ID to the other user's profile
                    emailProjectIDs.add(widget.projectIDs[widget.projectIndex]);
                    await FirebaseFirestore.instance
                        .collection('Profiles')
                        .doc(enteredEmail)
                        .update({"Project IDs": emailProjectIDs});
                    Navigator.of(context).pop();
                    // where adding the email to the specified project functionality would go
                  } else {
                    // User is already an assignee
                    showDialog(
                      context: context,
                      builder: (context) {
                        return AlertDialog(
                          title: const Text('Error'),
                          content: const Text("User is already an assignee"),
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
                  }
                }
              },
            ),
            TextButton(
              child: const Text('Cancel'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

The showAddAssigneesDialog method provides a user interface for adding a new assignee to a project by entering an email address. It checks for the validity of the email and whether the user is already assigned to the project, and updates the Firebase database accordingly. 


.. code-block:: dart

  void showErrorDialog(String message) {
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Error'),
          content: Text(message),
          actions: <Widget>[
            TextButton(
              child: const Text('OK'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

The showErrorDialog method provides a consistent way for the application to handle and display error messages to the user.


.. code-block:: dart

  Future<void> showEditDialog(BuildContext context) async {
    DateTime selectedDate = DateTime.now();

    TextEditingController projectNameController =
        TextEditingController(text: widget.project.projectName);
    TextEditingController leaderController =
        TextEditingController(text: widget.project.leader.split(": ").last);

    return showDialog<void>(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Edit Project'),
          content: SingleChildScrollView(
            child: Column(
              children: <Widget>[
                TextField(
                  controller: projectNameController,
                  onChanged: (value) {
                    widget.project.projectName = value;
                  },
                  decoration: const InputDecoration(labelText: 'Project Name'),
                ),
                const SizedBox(height: 16.0),
                const Text(
                  'Deadline:',
                  style: TextStyle(fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 8.0),
                TextButton(
                  onPressed: () async {
                    final DateTime? pickedDate = await showDatePicker(
                      context: context,
                      initialDate: selectedDate,
                      firstDate: DateTime.now(),
                      lastDate: DateTime(2101),
                    );

                    if (pickedDate != null && pickedDate != selectedDate) {
                      selectedDate = pickedDate;

                      widget.project.deadline =
                          '${pickedDate.day} ${getMonthName(pickedDate.month)} ${pickedDate.year}';
                    }
                  },
                  child: Text(
                    'Select Deadline',
                    style: TextStyle(color: Theme.of(context).primaryColor),
                  ),
                ),
                const SizedBox(height: 16.0),
                TextField(
                  controller: leaderController,
                  onChanged: (value) {
                    widget.project.leader = 'Leader: $value';
                  },
                  decoration: const InputDecoration(labelText: 'Leader'),
                ),
              ],
            ),
          ),
          actions: <Widget>[
            TextButton(
              child: const Text('Save'),
              onPressed: () async {
                // Check if the edited project name is empty or already exists
                if (widget.project.projectName.trim().isEmpty ||
                    widget.project.projectName.trim() == 'Project Name') {
                  showErrorDialog('Please enter a valid project name.');
                  return;
                }

                // Check if the edited project name already exists in Firebase
                final docSnapshot = await FirebaseFirestore.instance
                    .collection('Projects')
                    .where('Title', isEqualTo: widget.project.projectName)
                    .get();

                if (docSnapshot.docs.isNotEmpty &&
                    docSnapshot.docs.first.id !=
                        widget.projectIDs[widget.projectIndex]) {
                  showErrorDialog(
                      'There is already a project with that name in the database.');
                  return;
                }

                final projID = FirebaseFirestore.instance
                    .collection('Projects')
                    .doc(widget.projectIDs[widget.projectIndex]);
                projID.update({
                  "Title": widget.project.projectName,
                  "Deadline": widget.project.deadline,
                  "Project Leader": widget.project.leader
                });
                setState(() {});
                Navigator.of(context).pop();
              },
            ),
            TextButton(
              child: const Text('Cancel'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

The showEditDialog method provides a comprehensive interface for editing project details. The changes are made through the selectedDate variable which holds the project's deadline, the projectNameController which hold the project name, leaderController holds the name of the project's leader. When the changes are saved, then updates the Firestore database with the new data if the input fields are valid.

.. code-block:: dart

      Align(
            alignment: Alignment.bottomRight,
            child: PopupMenuButton<String>(
              icon: const Icon(Icons.more_vert),
              onSelected: (String value) {
                if (value == 'remove') {
                  showDeleteConfirmationDialog(context, widget.onDelete,
                      widget.projectIDs, widget.projectIndex, widget.email);
                } else if (value == 'edit') {
                  showEditDialog(context);
                } else if (value == 'add_assignees') {
                  showAddAssigneesDialog(context);
                }
              },
              itemBuilder: (BuildContext context) => <PopupMenuEntry<String>>[
                const PopupMenuItem<String>(
                  value: 'edit',
                  child: ListTile(
                    leading: Icon(Icons.edit),
                    title: Text('Edit'),
                  ),
                ),
                const PopupMenuItem<String>(
                  value: 'archive',
                  child: ListTile(
                    leading: Icon(Icons.archive),
                    title: Text('Archive'),
                  ),
                ),
                const PopupMenuItem<String>(
                  value: 'remove',
                  child: ListTile(
                    leading: Icon(Icons.delete),
                    title: Text('Remove'),
                  ),
                ),
                const PopupMenuItem<String>(
                  value: 'add_assignees',
                  child: ListTile(
                    leading: Icon(Icons.person_add),
                    title: Text('Add Assignees'),
                  ),
                ),
              ],
            ),
          ),

The Align widget in the _ProjectTileState class serves as a layout tool to precisely place the PopupMenuButton within the user interface of the ProjectTile widget. It ensures that the menu button is always located in the bottom-right corner of the tile, providing a consistent and intuitive interaction point for accessing additional options related to the project.


project_page.dart
--------------------





.. code-block:: dart  

  class ProjectsPage extends StatefulWidget {
    const ProjectsPage(
        {Key? key,
        required this.title,
        required this.email,
        required this.projectIDs,
        required this.projects,
        required this.settings,
        required this.profDetails,
        required this.activeColorScheme})
        : super(key: key);
  
    final String title;
    final String email;
    final List<dynamic> projectIDs;
    final List<Project> projects;
    final Map<String, dynamic> settings;
    final List<dynamic> profDetails;
    final ColorScheme activeColorScheme;
  
    @override
    State<ProjectsPage> createState() => _ProjectsPageState();
  }

This class ProjectsPage is used to display the list of projects the user created or is assigned to, the colour of the user interface depends on the color schemes selected. It contains the attributes title which is used as the header of the page, the user's email address, a list of the project ids which are used in the database , the list of project that only accepts instances of the class Project, settings contains the colorsheme information, profDetails, activeColorScheme which applies the colour scheme selected by the user.

.. code-block:: dart

  class _ProjectsPageState extends State<ProjectsPage> {
    _ProjectsPageState();
  
    Future accessProject(index, project) async {...}

    void deleteProject(project) {...}

    Future<void> showAddProjectDialog() async {...}

    void showErrorDialog(String message) {...}

    Future<bool> validateProjectDetails(Project newProject) async {...}

     @override
    Widget build(BuildContext context) {
      return Theme(
        data: ThemeData.from(colorScheme: widget.activeColorScheme),
        child: Scaffold(
          appBar: AppBar(
            backgroundColor: Theme.of(context).colorScheme.inversePrimary,
            title: Padding(
              padding: const EdgeInsets.symmetric(horizontal: 16.0),
              child: Text(widget.title,
                  textAlign: TextAlign.center,
                  style: const TextStyle(fontSize: 34)),
            ),
            automaticallyImplyLeading: false,
            centerTitle: true,
          ),
          body: Row(
            children: [
              Expanded(
                child: Padding(
                  padding: const EdgeInsets.all(16.0),
                  child: GridView.builder(
                      gridDelegate:
                          const SliverGridDelegateWithFixedCrossAxisCount(
                        crossAxisCount: 3,
                        crossAxisSpacing: 16.0,
                        mainAxisSpacing: 16.0,
                        childAspectRatio: 2,
                      ),
                      itemCount: widget.projects.length,
                      itemBuilder: (context, index) {
                        var project = widget.projects[index];
                        return GestureDetector(
                          onTap: () => accessProject(index, project),
                          child: ProjectTile(
                            project: project,
                            onDelete: () => deleteProject(project),
                            projectIDs: widget.projectIDs,
                            projectIndex: index,
                            email: widget.email,
                          ),
                        );
                      }),
                ),
              ),
            ],
          ),
          floatingActionButton: FloatingActionButton(
            onPressed: showAddProjectDialog,
            backgroundColor: Theme.of(context).colorScheme.inversePrimary,
            child: Icon(
              Icons.add,
              color: widget.activeColorScheme.secondary,
            ),
          ),
          floatingActionButtonLocation: FloatingActionButtonLocation.endFloat,
        ),
      );
    }
  }

The _ProjectsPageState is a state of the ProjectsPage that manages and displays dynamically the list of projects the user has. It has the accessProject, showAddProjectDialog methods and deleteProject, validateProjectDetails methods which use the showErrorDialog method.

.. code-block:: dart

  Future accessProject(index, project) async {
    List<dynamic> taskNames = [];
    List<dynamic> taskAssignees = [];
    List<dynamic> taskDescriptions = List.generate(100, (index) => '');
    List<DateTime?> deadlines = List.generate(100, (index) => null);
    int counter = 0;
    List<bool> isCardExpanded = [];
    FirebaseFirestore db = FirebaseFirestore.instance;
    final QuerySnapshot<Map<String, dynamic>> tasksQuery = await db
        .collection('Projects')
        .doc(widget.projectIDs[index])
        .collection('Tasks')
        .get();
    tasksQuery.docs.forEach((task) {
      if (task.id != "Placeholder Doc") {
        taskNames.add(task.id);
        taskDescriptions[counter] = task.get('Task Description');
        taskAssignees.add(task.get('Task Assignees'));
        isCardExpanded.add(false);
        if (task.get('Deadline') == null) {
          deadlines[counter] = task.get('Deadline');
        } else {
          deadlines[counter] = task.get('Deadline').toDate();
        }
        counter++;
      }
    });
    Navigator.push(
      context,
      MaterialPageRoute(
          builder: (context) => MyTasksPage(
                projectName: project.projectName,
                email: widget.email,
                taskNames: taskNames,
                taskAssignees: taskAssignees,
                taskDescriptions: taskDescriptions,
                deadlines: deadlines,
                counter: counter,
                isCardExpanded: isCardExpanded,
                projectID: widget.projectIDs[index],
                projects: widget.projects,
                profDetails: widget.profDetails,
                projectIDs: widget.projectIDs,
                settings: widget.settings,
                activeColorScheme: widget.activeColorScheme,
              )),
    );
  }

The accessProject method takes the index of the project in the list and the project object that it is selected by the user. The method retrieves task data from the Firestore database and processes, organizes, and prepares it to be displayed on the screen.

.. code-block:: dart

  void deleteProject(project) {
    setState(() {
      widget.projects.remove(project);
    });
  }

The deleteProject takes an instance of the class Project and removes it from the screen.


.. code-block:: dart

  Future<void> showAddProjectDialog() async {
      DateTime selectedDate = DateTime.now();
      Project newProject = Project();
  
      await showDialog<void>(
        context: context,
        builder: (BuildContext context) {
          return AlertDialog(
            title: const Text('Add Project'),
            content: SingleChildScrollView(
              child: Column(
                children: <Widget>[
                  TextField(
                    onChanged: (value) {
                      newProject.projectName = value;
                    },
                    decoration: const InputDecoration(labelText: 'Project Name'),
                  ),
                  const SizedBox(height: 16.0),
                  const Text(
                    'Deadline:',
                    style: TextStyle(fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 8.0),
                  TextButton(
                    onPressed: () async {
                      final DateTime? pickedDate = await showDatePicker(
                        context: context,
                        initialDate: selectedDate,
                        firstDate: DateTime.now(),
                        lastDate: DateTime(2101),
                      );
  
                      if (pickedDate != null && pickedDate != selectedDate) {
                        selectedDate = pickedDate;
                        newProject.deadline =
                            '${pickedDate.day} ${getMonthName(pickedDate.month)} ${pickedDate.year}';
                      }
                    },
                    child: const Text(
                      'Select Deadline',
                    ),
                  ),
                  const SizedBox(height: 16.0),
                  TextField(
                    onChanged: (value) {
                      newProject.leader = 'Leader: $value';
                    },
                    decoration: const InputDecoration(labelText: 'Leader'),
                  ),
                ],
              ),
            ),
            actions: <Widget>[
              TextButton(
                  child: const Text('Save'),
                  onPressed: () async {
                    if (await validateProjectDetails(newProject)) {
                      try {
                        setState(() {
                          widget.projects.add(newProject);
                        });
                        final projID = FirebaseFirestore.instance
                            .collection('Projects')
                            .doc();
                        await projID.set({
                          "Title": newProject.projectName,
                          "Deadline": newProject.deadline,
                          "Project Leader": newProject.leader,
                        });
                        widget.projectIDs.add(projID.id);
                        await FirebaseFirestore.instance
                            .collection('Profiles')
                            .doc(widget.email)
                            .update({"Project IDs": widget.projectIDs});
                        await projID
                            .collection('Tasks')
                            .doc("Placeholder Doc")
                            .set({"Title": "Placeholder"});
                        await projID
                            .collection('Tasks')
                            .doc("Placeholder Doc")
                            .collection('Tickets')
                            .doc('Placeholder Doc')
                            .set({"Title": "Placeholder"});
                        Navigator.of(context).pop();
                      } catch (e) {
                        showErrorDialog(
                            'Failed to save project data. Please try again later.');
                      }
                    }
                  }),
              TextButton(
                child: const Text('Cancel'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }


The showAddProjectDialog method in the _ProjectsPageState class provides a user interface for adding new projects. It displays a dialog where users can enter details such as the project name, leader, and deadline. The method includes validation to ensure the data entered is correct and unique, and upon successful validation, it saves the new project information to a Firestore database. Additionally, it updates the UI and the local state of the application to reflect the newly added project. 

.. code-block:: dart

  void showErrorDialog(String message) {
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Error'),
          content: Text(message),
          actions: <Widget>[
            TextButton(
              child: const Text('OK'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

The showErrorDialog function is used within the _ProjectsPageState class to display an error message to the user through an AlertDialog. It takes the messsage to be display as the input and it displays it to the console.

.. code-block:: dart

  Future<bool> validateProjectDetails(Project newProject) async {
    // Checks if the project name is empty or just has the default "Project Name"
    if (newProject.projectName.trim().isEmpty ||
        newProject.projectName.trim() == 'Project Name') {
      showErrorDialog('Please enter a valid project name.');
      return false;
    }

    // Checks if the project leader field is empty or just has the default "Leader: "
    if (newProject.leader.trim().isEmpty ||
        newProject.leader.trim() == 'Leader:') {
      showErrorDialog('Please enter a valid project leader.');
      return false;
    }

    // Checks if the project name already exists in Firebase
    final docSnapshot = await FirebaseFirestore.instance
        .collection('Projects')
        .where('Title', isEqualTo: newProject.projectName)
        .get();

    if (docSnapshot.docs.isNotEmpty) {
      showErrorDialog(
          'There is already a project with that name in the database.');
      return false;
    }

    return true;
  }

The validateProjectDetails method in the _ProjectsPageState class is designed to verify the integrity and uniqueness of a project's details before it is added to the database. This method takes a single parameter: newProject of type Project, which contains the details of the project to be validated. The method checks if the project's name and leader are valid (i.e., not empty or default placeholders) and whether the project name already exists in the Firestore database. The output of this method is a boolean value: it returns true if all validations pass, indicating the project details are valid and unique, and false otherwise. 


shared.dart
--------------------

imports
~~~~~~~~

.. code-block:: dart

  import 'package:flutter/material.dart';
  import 'package:cloud_firestore/cloud_firestore.dart';

The flutter material import is required for the Flutter framework. The firebase import is required for the Firestore database.


.. code-block:: dart

  String getMonthName(int month) {
    switch (month) {
      case 1:
        return 'January';
      case 2:
        return 'February';
      case 3:
        return 'March';
      case 4:
        return 'April';
      case 5:
        return 'May';
      case 6:
        return 'June';
      case 7:
        return 'July';
      case 8:
        return 'August';
      case 9:
        return 'September';
      case 10:
        return 'October';
      case 11:
        return 'November';
      case 12:
        return 'December';
      default:
        return '';
    }
  }

The method take a month as a parameter and it uses a switch statement to match the input month to its corresponding month name. Each case in the switch represents one of the twelve months. If the provided integer does not match any case from 1 to 12, the rethod returns an empty string.

.. code-block:: dart

  Future<void> showDeleteConfirmationDialog(
      BuildContext context,
      VoidCallback onDelete,
      List<dynamic> projectIDs,
      int projectIndex,
      String email) async {
    return showDialog<void>(
      context: context,
      barrierDismissible: false, // user must tap button!
      builder: (BuildContext context) {
        return AlertDialog(
          title: const Text('Confirm'),
          content: const SingleChildScrollView(
            child: ListBody(
              children: <Widget>[
                Text('Are you sure you want to delete this?'),
              ],
            ),
          ),
          actions: <Widget>[
            TextButton(
              child: const Text('Yes'),
              onPressed: () async {
                onDelete();
                final projID = FirebaseFirestore.instance
                    .collection('Projects')
                    .doc(projectIDs[projectIndex]);
                await projID.delete();
                projectIDs.removeAt(projectIndex);
                Navigator.of(context).pop();
              },
            ),
            TextButton(
              child: const Text('No'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

The showDeleteConfirmationDialog method is designed to securely confirm the deletion of a project. It takes several parameters: VoidCallback onDelete to execute delete the project, List<dynamic> projectIDs and an int projectIndex to identify and delete the specific project from the Firestore database, and a String email which is not used in the current implementation whihc is intended for notifications (future feature). The method displays a modal AlertDialog asking the user to confirm the deletion.













