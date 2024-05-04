Tasks page - task_cards.dart, task_page.dart
============================================

task_cards.dart
----------------
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
    );
  }
}

.. code-block:: dart
  void handleMenuSelection(String value) {
    switch (value) {
      case 'Edit':
        break;
      case 'Delete':
        break;
    }
  }




task_page.dart
---------------

..code-block:: dart
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




