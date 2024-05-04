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

This class is used in the projects_page.dart file to create Project


project_tiles.dart
--------------------
imports
-------

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



