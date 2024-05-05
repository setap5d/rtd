Tasks page - task_cards.dart, task_page.dart
============================================

task_cards.dart
----------------

import
~~~~~~~

.. code-block:: dart

  import 'package:flutter/material.dart';

This import statement brings in the Material Design library from Flutter.

.. code-block:: dart

  class TaskCard extends StatelessWidget {
    final String taskName;
    final String deadline;
    final String taskAssignees;
    final String taskDescription;
    final List<String> ticketNames;
    final double width;
    final double height;
    final bool isCardExpanded;
    final Function(BuildContext context, int index) addTicket;
    final int index;
    final ColorScheme activeColorScheme;
  
    const TaskCard({
      Key? key,
      required this.taskName,
      required this.deadline,
      required this.taskAssignees,
      required this.taskDescription,
      required this.ticketNames,
      required this.width,
      required this.height,
      required this.isCardExpanded,
      required this.addTicket,
      required this.index,
      required this.activeColorScheme,
    }) : super(key: key);
  
    void handleMenuSelection(String value) {...}
  
    @override
    Widget build(BuildContext context) {
      return Theme(
        data: ThemeData.from(colorScheme: activeColorScheme),
        child: Expanded(...),
      );
    }
  }

The TaskCard class is a StatelessWidget designed to display details of a task. It shows information such as the task name, assignees, description, and deadline, controlled by attributes like taskName, taskAssignees, taskDescription, and deadline. Additional attributes include activeColorScheme used to apply the color scheme selected by the user.

.. code-block:: dart

  void handleMenuSelection(String value) {
    switch (value) {
      case 'Edit':
        break;
      case 'Delete':
        break;
    }
  }

The handleMenuSelection method in the TaskCard class is designed to process user selections from a popup menu specifically for 'Edit' and 'Delete' actions. It takes a single parameter, value, which is a string representing the user's menu choice. 

.. code-block:: dart

  child: Expanded(
            child: Stack(
              children: [
                Positioned.fill(
                  child: Container(
                    width: width,
                    height: height,
                    decoration: BoxDecoration(
                      borderRadius: BorderRadius.circular(10.0),
                      border: Border.all(
                        color: const Color.fromARGB(255, 170, 170, 170),
                        width: 0.5,
                      ),
                    ),
                    child: Row(
                      mainAxisAlignment: MainAxisAlignment.spaceBetween,
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Expanded(
                          child: Padding(
                            padding: const EdgeInsets.all(8.0),
                            child: Column(
                              crossAxisAlignment: CrossAxisAlignment.start,
                              children: [
                                Text(
                                  'Task Name: $taskName',
                                  style: const TextStyle(
                                    fontWeight: FontWeight.bold,
                                    fontSize: 17,
                                  ),
                                  overflow:
                                      isCardExpanded ? null : TextOverflow.ellipsis,
                                ),
                                Text(
                                  'Task Assignees: $taskAssignees',
                                  style: const TextStyle(
                                    fontSize: 15,
                                  ),
                                  overflow:
                                      isCardExpanded ? null : TextOverflow.ellipsis,
                                ),
                                Visibility(
                                  visible: isCardExpanded,
                                  child: Column(
                                    crossAxisAlignment: CrossAxisAlignment.start,
                                    children: [
                                      Text(
                                        'Task Description: $taskDescription',
                                        style: const TextStyle(
                                          fontSize: 15,
                                        ),
                                      ),
                                      const SizedBox(height: 8),
                                      // Display ticket names here
                                      if (ticketNames.isNotEmpty)
                                        ...ticketNames.map((ticketName) => Text(
                                              'Ticket: $ticketName',
                                              style: const TextStyle(
                                                fontSize: 15,
                                                fontWeight: FontWeight.bold,
                                              ),
                                            )),
                                    ],
                                  ),
                                ),
                                if (isCardExpanded) const Spacer(),
                                if (isCardExpanded)
                                  ElevatedButton(
                                    onPressed: () {
                                      addTicket(context, index);
                                    },
                                    child: const Text('Add Ticket'),
                                  ),
                              ],
                            ),
                          ),
                        ),
                        Container(
                          padding: const EdgeInsets.symmetric(
                              horizontal: 20, vertical: 10),
                          decoration: BoxDecoration(
                            color: Theme.of(context).colorScheme.inversePrimary,
                            borderRadius: BorderRadius.circular(5),
                          ),
                          child: SizedBox(
                            width: 200,
                            child: Column(
                              mainAxisAlignment: MainAxisAlignment.center,
                              children: [
                                Text(
                                  'Deadline',
                                  style: TextStyle(
                                    color: activeColorScheme.onBackground,
                                    fontWeight: FontWeight.bold,
                                    fontSize: 15,
                                  ),
                                ),
                                const SizedBox(height: 4.0),
                                Text(
                                  deadline,
                                  style: TextStyle(
                                    color: activeColorScheme.onBackground,
                                    fontSize: 15,
                                  ),
                                ),
                              ],
                            ),
                          ),
                        ),
                      ],
                    ),
                  ),
                ),
                Positioned(
                  top: 5,
                  right: 5,
                  child: PopupMenuButton<String>(
                    onSelected: handleMenuSelection,
                    itemBuilder: (BuildContext context) => [
                      const PopupMenuItem<String>(
                        value: 'Edit',
                        child: Text('Edit'),
                      ),
                      const PopupMenuItem<String>(
                        value: 'Delete',
                        child: Text('Delete'),
                      ),
                    ],
                    icon: const Icon(Icons.more_vert),
                  ),
                ),
              ],
            ),
          ),

This code snippet displays task details like name, assignees, description, and deadline. The card also provides functionality to edit or delete tasks through a popup menu button. The layout adjusts based on whether the card is expanded or not, showing additional details like ticket names when expanded. 

task_page.dart
---------------

imports
~~~~~~~~

.. code-block:: dart

  import 'package:flutter/material.dart';
  import 'task_cards.dart';
  import 'package:cloud_firestore/cloud_firestore.dart';
  import 'project_format.dart';

This first import brings in the Material Design library from Flutter, providing UI components and styling options. The second import imports the TaskCard class which is used to display the tasks in the task page. The third import adds the Firestore database library to retrieve and add tasks to the database. This last import is used to access the project that contain the tasks.

.. code-block:: dart

  class MyTasksPage extends StatefulWidget {
    const MyTasksPage(
        {Key? key,
        required this.projectName,
        required this.email,
        required this.taskNames,
        required this.taskAssignees,
        required this.taskDescriptions,
        required this.deadlines,
        required this.counter,
        required this.isCardExpanded,
        required this.projects,
        required this.profDetails,
        required this.projectID,
        required this.projectIDs,
        required this.settings,
        required this.activeColorScheme})
        : super(key: key);
  
    final String projectName;
    final String email;
    final List<dynamic> taskNames;
    final List<dynamic> taskAssignees;
    final List<dynamic> taskDescriptions;
    final List<DateTime?> deadlines;
    final int counter;
    final List<bool> isCardExpanded;
    final String projectID;
    final List<dynamic> projectIDs;
    final List<Project> projects;
    final List<dynamic> profDetails;
    final Map<String, dynamic> settings;
    final ColorScheme activeColorScheme;
  
    @override
    State<MyTasksPage> createState() => _MyTasksPageState();
  }

The MyTasksPage class is a StatefulWidget in Flutter designed to manage and display detailed information about tasks for a specific project. It takes the projectName, email, projectID parameter, identify the project and the user that it's logged in. The TaskNames, taskAssignees, taskDescriptions store dynamic data for each task.

.. code-block:: dart

  class _MyTasksPageState extends State<MyTasksPage> {
    _MyTasksPageState();
    int _counter = 0;
    final _formKey = GlobalKey<FormState>();
    TextEditingController taskNameController = TextEditingController();
    TextEditingController taskAssigneesController = TextEditingController();
    TextEditingController taskDescriptionController = TextEditingController();
    List<List<String>> ticketNamesList = [];
    List<List<String>> ticketDescriptionsList = [];
    List<bool> ticketChecked = [];
  
    void addTicket(BuildContext context, int index) async {...}

    void addNewTask(BuildContext context) {...}

    void initState() {...}

    Future<void> selectDate(BuildContext context, int index) async {...}

    void incrementCounter() {...}

    void triggerRebuild() {...}

    void decrementIndex(int index) {...}

    @override
    Widget build(BuildContext context) {...}
}

    
The _MyTasksPageState class is the state management component for the MyTasksPage widget in Flutter, designed to handle the functionality related to displaying and managing tasks within a specific project. It uses several controllers (taskNameController, taskAssigneesController, taskDescriptionController) to manage form inputs and stores lists of ticket names and descriptions to handle dynamic task details. The class includes functionality to add new tickets to tasks (addTicket), create new tasks (addNewTask), and choose deadlines (selectDate). It maintains a counter (_counter) to track the number of tasks and uses a _formKey for form validation.

.. code-block:: dart

  class TaskButtonField extends StatelessWidget {
    const TaskButtonField({
      super.key,
      required this.widget,
      required this.text,
      required this.onPressed  
      });
  
    final MyTasksPage widget;
    final String text;
    final VoidCallback onPressed;
  
    @override
    Widget build(BuildContext context) {
      return Padding(
        padding: const EdgeInsets.all(8),
        child: ElevatedButton(
          style: ButtonStyle(
              backgroundColor:
                  MaterialStateProperty.all<Color>(
            widget.activeColorScheme.inversePrimary,
          )),
          onPressed: onPressed,
          child: Text(text,
              style: TextStyle(
                  color: widget.activeColorScheme.onBackground)),
        ),
      );
    }
  }

.. code-block:: dart

  class TaskTextField extends StatelessWidget {
    const TaskTextField({
      super.key,
      required this.widget,
      required this.taskNameController,
      required this.fieldName,
      required this.errorMessage
    });
  
    final MyTasksPage widget;
    final TextEditingController taskNameController;
    final String fieldName;
    final String errorMessage;
  
    @override
    Widget build(BuildContext context) {
      return Padding(
        padding: const EdgeInsets.all(8),
        child: TextFormField(
          style: TextStyle(
              color: widget.activeColorScheme.onBackground),
          controller: taskNameController,
          decoration: InputDecoration(
            labelStyle: TextStyle(
                color: widget.activeColorScheme.onBackground),
            labelText: fieldName,
            border: const OutlineInputBorder(),
          ),
          validator: (value) {
            if (value == null || value.isEmpty) {
              return errorMessage;
            }
            return null;
          },
        ),
      );
    }
  }






    
    
    







