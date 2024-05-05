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

    @override
      Widget build(BuildContext context) {
        final screenWidth = MediaQuery.of(context).size.width;
    
        return Theme(
          data: ThemeData.from(colorScheme: widget.activeColorScheme),
          child: Scaffold(
            appBar: AppBar(
              title: Text(widget.projectName),
            ),
            body: Row(
              children: [
                Column(
                  mainAxisAlignment: MainAxisAlignment.start,
                  children: [
                    Expanded(
                      child: Container(
                        height: 700,
                        width: screenWidth,
                        decoration: BoxDecoration(
                          borderRadius: BorderRadius.circular(10.0),
                          border: Border.all(
                            color: Theme.of(context).colorScheme.inversePrimary,
                            width: 0.5,
                          ),
                        ),
                        child: ListView.builder(
                          itemCount: _counter,
                          itemBuilder: (context, index) {
                            double cardHeight =
                                widget.isCardExpanded[index] ? 300.0 : 70.0;
                            return Container(
                              constraints: BoxConstraints(maxHeight: cardHeight),
                              child: InkWell(
                                onTap: () async {
                                  if (ticketChecked[index] == false) {
                                    FirebaseFirestore db =
                                        FirebaseFirestore.instance;
                                    final QuerySnapshot<Map<String, dynamic>>
                                        tasksQuery = await db
                                            .collection('Projects')
                                            .doc(widget.projectID)
                                            .collection('Tasks')
                                            .doc(widget.taskNames[index])
                                            .collection('Tickets')
                                            .get();
                                    for (var ticket in tasksQuery.docs) {
                                      if (ticket.id != "Placeholder Doc") {
                                        ticketNamesList[index].add(ticket.id);
                                        ticketDescriptionsList[index]
                                            .add(ticket.get('Ticket Description'));
                                      }
                                    }
                                    ticketChecked[index] = true;
                                    await Future.delayed(
                                        const Duration(milliseconds: 100));
                                  }
                                  setState(() {
                                    widget.isCardExpanded[index] =
                                        !widget.isCardExpanded[index];
                                  });
                                },
                                child: AnimatedContainer(
                                  duration: const Duration(milliseconds: 0),
                                  height: cardHeight,
                                  child: Column(
                                    children: [
                                      TaskCard(
                                        height: cardHeight,
                                        taskName: '${widget.taskNames[index]}',
                                        deadline: widget.deadlines[index] != null
                                            ? '${widget.deadlines[index]!.day}/${widget.deadlines[index]!.month}/${widget.deadlines[index]!.year}'
                                            : 'No Deadline Set',
                                        taskAssignees:
                                            '${widget.taskAssignees[index]}',
                                        taskDescription:
                                            '${widget.taskDescriptions[index]}',
                                        ticketNames: ticketNamesList[index],
                                        width: 300,
                                        isCardExpanded:
                                            widget.isCardExpanded[index],
                                        addTicket: addTicket,
                                        index: index,
                                        activeColorScheme: widget.activeColorScheme,
                                      ),
                                    ],
                                  ),
                                ),
                              ),
                            );
                          },
                        ),
                      ),
                    ),
                  ],
                ),
              ],
            ),
            floatingActionButton: FloatingActionButton(
              backgroundColor: widget.activeColorScheme.inversePrimary,
              onPressed: () async {
                await showDialog<void>(
                  context: context,
                  builder: (context) => AlertDialog(
                    backgroundColor: widget.activeColorScheme.background,
                    content: Stack(
                      clipBehavior: Clip.none,
                      children: <Widget>[
                        Positioned(
                          right: -40,
                          top: -40,
                          child: InkResponse(
                            onTap: () {
                              Navigator.of(context).pop();
                            },
                            child: CircleAvatar(
                              backgroundColor: widget.activeColorScheme.primary,
                              child: const Icon(Icons.close),
                            ),
                          ),
                        ),
                        Container(
                          color: widget.activeColorScheme.background,
                          child: Form(
                            key: _formKey,
                            child: Column(
                              mainAxisSize: MainAxisSize.min,
                              children: <Widget>[
                                TaskTextField(widget: widget, taskNameController: taskNameController, fieldName: "Task Name", errorMessage: "Please enter task name",),
                                TaskTextField(widget: widget, taskNameController: taskAssigneesController, fieldName: "Task Assignees", errorMessage: "Please enter task assignees",),
                                TaskTextField(widget: widget, taskNameController: taskDescriptionController, fieldName: "Task Description", errorMessage: "Please enter task description",),
                                TaskButtonField(widget: widget, text: "Set Deadline", onPressed: () => selectDate(context, _counter),),
                                TaskButtonField(widget: widget, text: "Submit", onPressed: () => addNewTask(context),),
                              ],
                            ),
                          ),
                        ),
                      ],
                    ),
                  ),
                );
              },
              tooltip: 'New Task',
              child: Icon(
                Icons.add,
                color: widget.activeColorScheme.secondary,
              ),
            ),
          ),
        );
      }
    }

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






    
    
    







