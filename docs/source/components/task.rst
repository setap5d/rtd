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

  void addTicket(BuildContext context, int index) async {
      final TextEditingController ticketNameController = TextEditingController();
      final TextEditingController ticketDescriptionController = TextEditingController();
  
      await showDialog<void>(
        context: context,
        builder: (context) => AlertDialog(
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
                    backgroundColor: Colors.red,
                    child: Icon(Icons.close),
                  ),
                ),
              ),
              Form(
                key: _formKey,
                child: Column(
                  mainAxisSize: MainAxisSize.min,
                  children: <Widget>[
                    Padding(
                      padding: const EdgeInsets.all(8),
                      child: TextFormField(
                        controller: ticketNameController,
                        decoration: const InputDecoration(
                          labelText: 'Ticket Name',
                          border: OutlineInputBorder(),
                        ),
                        validator: (value) {
                          if (value == null || value.isEmpty) {
                            return 'Please enter a ticket name';
                          }
                          return null;
                        },
                      ),
                    ),
                    Padding(
                      padding: const EdgeInsets.all(8),
                      child: TextFormField(
                        controller: ticketDescriptionController,
                        decoration: const InputDecoration(
                          labelText: 'Ticket Description',
                          border: OutlineInputBorder(),
                        ),
                        validator: (value) {
                          if (value == null || value.isEmpty) {
                            return 'Please enter a ticket description';
                          }
                          return null;
                        },
                      ),
                    ),
                    Padding(
                      padding: const EdgeInsets.all(8),
                      child: ElevatedButton(
                        onPressed: () {
                          if (_formKey.currentState!.validate()) {
                            _formKey.currentState!.save();
  
                            ticketNamesList[index].add(ticketNameController.text);
                            ticketDescriptionsList[index].add(ticketDescriptionController.text);
  
                            FirebaseFirestore db = FirebaseFirestore.instance;
                            DocumentReference ticketRef = db
                                .collection('Projects')
                                .doc(widget.projectID)
                                .collection('Tasks')
                                .doc(widget.taskNames[index])
                                .collection('Tickets')
                                .doc(ticketNameController.text);
  
                            ticketRef.set({
                              "Ticket Description": ticketDescriptionController.text,
                            });
  
                            Navigator.of(context).pop();
                          }
                        },
                        child: const Text('Submit'),
                      ),
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
      );
    }

The addTicket method in the _MyTasksPageState class facilitates adding a new ticket to a specific task within a project.This method takes the index of the task to which the ticket will be added. It presents the user with a form in an AlertDialog to enter the ticket's name and description. Upon form validation, it updates the respective lists for ticket names and descriptions, saves the data to Firestore under the specified task and project, and then closes the dialog.

.. code-block:: dart

  void addNewTask(BuildContext context) {
      if (_formKey.currentState!.validate()) {
        _formKey.currentState!.save();
        if (_counter < widget.taskDescriptions.length) {
          widget.taskDescriptions[_counter] = taskDescriptionController.text;
        } else {
          widget.taskDescriptions.add(taskDescriptionController.text);
        }
        Navigator.of(context).pop();
        incrementCounter();
  
        FirebaseFirestore db = FirebaseFirestore.instance;
        DocumentReference taskRef = db
            .collection('Projects')
            .doc(widget.projectID)
            .collection("Tasks")
            .doc(taskNameController.text);
        taskRef.set({
          "Task Assignees": taskAssigneesController.text,
          "Task Description": taskDescriptionController.text,
          "Deadline": widget.deadlines[_counter - 1]
        });
  
        taskNameController.clear();
        taskAssigneesController.clear();
        taskDescriptionController.clear();
        // Navigator.of(context).pop();
      }
    }

The addNewTask method in the _MyTasksPageState class is responsible for adding new tasks to a project. The method starts by validating the form inputs via a form key (_formKey). Upon successful validation, it updates the task descriptions list with the new task's description. It then increments the task counter, manages navigation to pop the dialog, and commits the new task data to Firestore under the specific project ID and task name. The task data includes assignees, description, and the latest deadline. After saving the data, it clears the input fields for reuse.


.. code-block:: dart

    void initState() {
      _counter = widget.counter;
      super.initState();
      ticketNamesList = List.generate(100, (index) => []);
      ticketDescriptionsList = List.generate(100, (index) => []);
      ticketChecked = List.generate(100, (index) => false);
    }

The initState method in the _MyTasksPageState class is an initialization lifecycle hook that sets up initial state values for the widget. It starts by setting _counter to the value of counter passed from the parent widget, ensuring that the task count is correctly initialized. This method then proceeds to initialize three lists using List.generate: ticketNamesList and ticketDescriptionsList are set up to store names and descriptions for up to 100 tickets, respectively, each initialized as empty lists. Similarly, ticketChecked is initialized with 100 boolean values set to false, indicating that no tickets have been checked yet. 

.. code-block:: dart

    Future<void> selectDate(BuildContext context, int index) async {
      final DateTime? picked = await showDatePicker(
        context: context,
        initialDate: DateTime.now(),
        firstDate: DateTime.now(),
        lastDate: DateTime(2101),
      );
      if (picked != null && picked != widget.deadlines[index]) {
        setState(() {
          widget.deadlines[index] = picked;
        });
      }
    }

The selectDate method is an asynchronous function that allows the user to pick a date using a date picker dialog. The method takes the index of the task's deadline to be updated.  Within the method, showDatePicker displays the date picker dialog, initialized with the current date as the initial date and allowing selection of dates between today and the year 2101. When the user selects a date (picked), it updates the deadline of the task at the specified index in the widget.deadlines list, triggering a state update using setState. This method does not return anything explicitly, as it updates the state of the widget directly.

.. code-block:: dart

    void incrementCounter() {
      setState(() {
        _counter++;
        widget.isCardExpanded.add(false);
        widget.taskNames.add(taskNameController.text);
        widget.taskAssignees.add(taskAssigneesController.text);
        widget.taskDescriptions.add(taskDescriptionController.text);
        widget.deadlines.add(null);
      });
    }

The incrementCounter method in the _MyTasksPageState class is a utility function designed to manage the addition of new task-related data to the state of the MyTasksPage widget. This method does not take any parameters nor does it return any value, as its primary function is to update the state of the widget. When called, it increments a counter (_counter) used to track the number of tasks. Simultaneously, it adds default or new values to various lists that store task information, such as isCardExpanded, taskNames, taskAssignees, taskDescriptions, and deadlines. This addition includes setting new task details from the form, adding a default false value to isCardExpanded to keep the task initially collapsed, and appending a null to deadlines indicating no deadline set.

.. code-block:: dart

    void triggerRebuild() {
      //triggers rebuild
      setState(() {});
    }
  
    void decrementIndex(int index) {
      //decrements the counter when a task is deleted
      _counter--;
      triggerRebuild();
    }

The triggerRebuild and decrementIndex methods in the _MyTasksPageState class facilitate dynamic UI updates within a task management context. The triggerRebuild method employs setState() to prompt a UI refresh without altering any state data directly. Conversely, decrementIndex is specifically used when a task is removed; it decrements a task counter (_counter) and calls triggerRebuild to update the UI accordingly. 

.. code-block:: dart

    @override
    Widget build(BuildContext context) {
      final screenWidth = MediaQuery.of(context).size.width;
  
      WidgetsBinding.instance.addPostFrameCallback((_) {
        triggerRebuild.call();
      });
  
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
                        //call this to rebuild after deleting or updating a project
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
                                      isCardExpanded: widget.isCardExpanded,
                                      addTicket: addTicket,
                                      index: index,
                                      activeColorScheme: widget.activeColorScheme,
                                      formKey: _formKey,
                                      deadlines: widget.deadlines,
                                      projectID: widget.projectID,
                                      counter: _counter,
                                      taskNames: widget.taskNames,
                                      taskDescriptions: widget.taskDescriptions,
                                      allTaskAssignees: widget.taskAssignees,
                                      decrementIndex: decrementIndex,
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
          floatingActionButton: FloatingActionButton(...)
          ),
        );
      }
    }

This method is used to build the widget. It utilizes a ListView.builder to create a list of expandable TaskCard widgets, each representing a task. The number of cards is determined by _counter, and each card can expand or collapse to show more or less detail about the task, including descriptions and associated tickets.The list updates in response to interactions, such as tapping a card to expand it, which may trigger fetching ticket details from a Firestore database if they haven't been loaded previously.

.. code-block:: dart

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
                              TaskTextField(
                                widget: widget,
                                taskNameController: taskNameController,
                                fieldName: "Task Name",
                                errorMessage: "Please enter task name",
                              ),
                              TaskTextField(
                                widget: widget,
                                taskNameController: taskAssigneesController,
                                fieldName: "Task Assignees",
                                errorMessage: "Please enter task assignees",
                              ),
                              TaskTextField(
                                widget: widget,
                                taskNameController: taskDescriptionController,
                                fieldName: "Task Description",
                                errorMessage: "Please enter task description",
                              ),
                              TaskButtonField(
                                widget: widget,
                                text: "Set Deadline",
                                onPressed: () => selectDate(context, _counter),
                              ),
                              TaskButtonField(
                                widget: widget,
                                text: "Submit",
                                onPressed: () => addNewTask(context),
                              ),
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


The FloatingActionButton in this snippet is used to add new tasks within the MyTasksPage. When pressed, it triggers an asynchronous function that presents a dialog containing a form for task creation. Additional functionality within the dialog includes a button to set a task deadline using a date picker, and a submit button that calls addNewTask(context) to save the new task to the system. This process involves not only capturing user input but also handling data validation and updating the application's state and backend with the new task details. Succcessfully added tasks are stored in the database.


.. code-block:: dart

  class TaskButtonField extends StatelessWidget {
    const TaskButtonField(
        {super.key,
        required this.widget,
        required this.text,
        required this.onPressed});
  
    final MyTasksPage widget;
    final String text;
    final VoidCallback onPressed;
  
    @override
    Widget build(BuildContext context) {
      return Padding(
        padding: const EdgeInsets.all(8),
        child: ElevatedButton(
          style: ButtonStyle(
              backgroundColor: MaterialStateProperty.all<Color>(
            widget.activeColorScheme.inversePrimary,
          )),
          onPressed: onPressed,
          child: Text(text,
              style: TextStyle(color: widget.activeColorScheme.onBackground)),
        ),
      );
    }
  }

The TaskButtonField class encapsulates a button widget tailored for tasks. It takes in parameters such as the parent widget (MyTasksPage), the button text (text), and the callback function to be executed when the button is pressed (onPressed).

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

The TaskTextField class is a StatelessWidget designed to encapsulate a text input field specifically tailored for task-related forms.  It accepts several parameters for customization and functionality: widget, which refers to the parent widget (MyTasksPage) providing access to the current theme's color scheme; taskNameController, a TextEditingController that manages the text input within the field; fieldName, a string that labels the input field; and errorMessage, a string displayed when validation fails. In its build method, it constructs a TextFormField styled according to the parent widget's color scheme and includes a validator function that checks if the input is empty, returning an error message if so. 





    
    
    







