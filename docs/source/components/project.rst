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

The flutter material import is required for the Flutter framework. The three firebase imports are required for the Firebase authentication and Firestore database. The project_format.dart file is imported to format the project details. The shared.dart imports two functions that are used by the file.

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

The project file class is to inititialize the fromtend of a project's information. It takes the "The ProjectTile class is used to initialize the frontend representation of a project's information in a Flutter application. It incorporates an instance of the Project class, which manages the project's backend data. Alongside, it handles a deletion functionality through the onDelete callback, manages a list of project IDs (projectIDs), and utilizes a specific project index (projectIndex) to differentiate between projects. The email parameter represents the contact information of the project leader or manager." This override of createState() returns an instance of _ProjectTileState, which actually display the project.

.. code-block:: dart
class _ProjectTileState extends State<ProjectTile> {

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
        ],
      ),
    );
  }
}







