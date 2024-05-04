Profile page - profile.dart
===========================

imports
-------

.. code-block::
    
    import 'dart:io';
    import 'package:file_picker/file_picker.dart';
    import 'package:cloud_firestore/cloud_firestore.dart';
    import 'package:firebase_storage/firebase_storage.dart';
    import 'package:flutter/material.dart';

These imports are required for the Flutter framework to function, and for the Firebase services to be used.
Additionally, the file_picker package is used to allow the user to select a profile picture from their device.

ProfilePage
------------

.. code-block::
    
    class ProfilePage extends StatefulWidget {
        const ProfilePage(
            {required this.email, required this.profDetails, super.key});
        final String email;
        final List<dynamic> profDetails;

        @override
        State<ProfilePage> createState() => _ProfilePageState();
    }

The ProfilePage class is a StatefulWidget. It takes in the user's email and profile details as parameters. 
The profile details are stored in a list of dynamic objects. The class returns an instance of the 
_ProfilePageState class.

_ProfilePageState
-----------------

.. code-block::

    class _ProfilePageState extends State<ProfilePage> {
        String fName = '';
        String lName = '';
        String email = '';
        String phoneNumber = '';
        String skills = '';
        String errMessage = '';
        PlatformFile? selectedImage;
        UploadTask? uploadTask;
        late String urlDownload = "";
        bool newImage = false

Here we define the state of the ProfilePage class. We define the user's first name, last name, email, phone number, and
skills. We also define the attributes required for profile picture upload. 

* The selectedImage attribute is used to store the image file that the user selects from their device in local storage.
* The uploadTask attribute is used to allow the chosen image to be uploaded to Firebase Storage before creating a download URL for it.
* The urlDownload attribute is used to store the download URL of the user's profile picture.
* The newImage attribute is used to determine whether the user has selected a new profile picture.

.. code-block::

    Future uploadImage() async {
        final path = 'files/${selectedImage!.name}'; // Change this to firebase path
        final file = File(selectedImage!.path!);

        // Add firebase upload here
        final ref = FirebaseStorage.instance.ref().child(path);
        setState(() {
        uploadTask = ref.putFile(file);
        });

        final snapshot = await uploadTask!.whenComplete(() {});

        urlDownload = await snapshot.ref.getDownloadURL();

        setState(() {
        uploadTask = null;
        newImage = true;
        });
    }

This function is used to upload the user's profile picture to Firebase Storage. It takes the image from the selected 
path and uploads it to the Firebase Storage path. The download URL of the image is then stored in the 
urlDownload attribute for later fetching. Afterwards, the newImage attribute is set to true to indicate that a new image has been 
uploaded and uploadTask is set to null to show that nothing is currently being uploaded.

.. code-block::

    Future selectImage() async {
        final result = await FilePicker.platform.pickFiles(
            type: FileType.image,
            allowMultiple: false,
        );
        if (result == null) return;

        setState(() {
            selectedImage = result.files.first;
        });
    }

This function is used to allow the user to select a profile picture from their device. It uses the file_picker package 
to allow the user to select a file that can only be an image. The selected image is then stored in the selectedImage 
attribute. 

.. code-block::

    void checkInputs(String fName, String lName, String email, String phoneNumber,
      String skills) {
        FirebaseFirestore db = FirebaseFirestore.instance;
        setState(() {
            errMessage = '';
            if (isValidName(fName, lName) == true &&
                isValidEmail(email) == true &&
                isValidPhoneNumber(phoneNumber) == true &&
                isValidSkills(skills) == true) {
                DocumentReference profileRef =
                    db.collection('Profiles').doc(email.toLowerCase());
                profileRef.update({
                    "First Name": fName,
                    "Last Name": lName,
                    "Phone Number": phoneNumber,
                    "Skills": skills
                });
                if (newImage == true) {
                    DocumentReference pfpRef = db
                    .collection('Profiles')
                    .doc(
                        email) // This is looking in the db for the input email that the user has entered, need to change to user id inherited from login
                    .collection('User')
                    .doc('ProfilePic');
                    pfpRef.update({"Download URL": urlDownload});
                }
            } else {}
        });
    }

The checkInputs method is used to validate the user's input. It takes in the user's first name, last name, email, 
phone number, and skills as parameters. It then calls a function for each parameter to check if they are valid. 
If all checks pass, the database is updated with the user's profile details. If a new image has been uploaded, 
the database is updated with the download URL of the new profile picture.

.. code-block::

    bool isValidName(String fName, String lName) {
        if (fName.isEmpty || lName.isEmpty) {
            errMessage = 'Error: First and last name required';
            return false;
        }

        if (!RegExp(r'^[a-zA-Z]+$').hasMatch(fName) ||
            !RegExp(r'^[a-zA-Z]+$').hasMatch(lName)) {
            errMessage = 'Error: First and last name may only contain letters (A-Z)';
            return false;
        }

        if (!(fName.length >= 2 && fName.length <= 20) ||
            !(lName.length >= 2 && lName.length <= 20)) {
            errMessage =
                'Error: First or last name must be between 2 and 20 characters';
            return false;
        }

        return true;
    }

    bool isValidEmail(String email) {
        if (email.isEmpty) {
            errMessage = 'Error: Email required';
            return false;
        }

        // Check for the presence of '@' and '.'
        if (!email.contains('@') || !email.contains('.')) {
            errMessage = 'Error: Email must contain @ and .';
            return false;
        }

        // Check that '@' comes before '.'
        if (email.indexOf('@') > email.lastIndexOf('.')) {
            errMessage = 'Error: Email is invalid';
            return false;
        }

        // Check that there's at least one character before and after '@'
        if (email.indexOf('@') == 0 || email.indexOf('@') == email.length - 1) {
            errMessage = 'Error: Email is invalid';
            return false;
        }

        return true;
    }

    bool isValidPhoneNumber(String phoneNumber) {
        // Check if the numeric phone number has at least 10 digits (adjust as needed)
        if (phoneNumber.isNotEmpty) {
            if (!phoneNumber.contains(RegExp(r'^[0-9]+$'))) {
                errMessage = 'Error: Phone number may only contain numbers';
                return false;
            }
            if (phoneNumber.length < 10) {
                errMessage = 'Error: Phone number must be at least 10 digits';
                return false;
            }
        }
        return true;
    }

    bool isValidSkills(String skills) {
        if (!(skills.length >= 10 && skills.length <= 100)) {
            errMessage = 'Error: Skills are not between 10 and 100 characters';
            return false;
        }
        return true;
    }

These functions are used to validate the user's input for each field. They take in the user's input as a parameter for 
each of them. All of the individual functions return a boolean value. If the input is valid, the function returns true.
Once all functions have been called, if they all return true then the database is updated with the user inputs. 

The isValidName function checks if the first and last name are valid. They are both required fields. A regular
expression is used for both inputs to ensure they only contains characters in the English alphabet. The length of both 
the first and last name must be between 2 and 20 characters. If any of these conditions are not met, the function 
returns false.

The isValidEmail function checks if the email is valid. An email is required. The function checks if the email 
contains an '@' and a '.'. It also checks if the '@' comes before the '.'. The function also checks if there is at 
least one character before and after the '@'. This is to ensure that an email is present and valid. If any of these 
conditions are not met, the function returns false.

The isValidPhoneNumber function checks if the phone number is valid. A phone number is not required. If a phone number 
is entered, a regular expression check is used to ensure that the phone number only contains numbers. The phone number 
input must be less than 10 digits long. If any of these conditions are not met, the function returns false.

The isValidSkills function checks if the skills input is valid. The skills input is not required. 
The length of the skills field must be between 10 and 100 characters. If this condition is not met, the function 
returns false.

.. code-block::

    @override
    void initState() {
        super.initState();
        fName = widget.profDetails[0];
        lName = widget.profDetails[1];
        email = widget.profDetails[2];
        phoneNumber = widget.profDetails[3];
        skills = widget.profDetails[4];
    }

The initState method is used to set the initial state of the ProfilePage class. The user's first name, last name, email, 
phone number, and skills are set to the values stored in the profDetails list.

.. code-block::

    @override
    Widget build(BuildContext context) {
        return Scaffold(

            ...

        child: ElevatedButton(
            onPressed: selectImage,
            child: const Text('Select Image'),
        ),

            ...

        child: ElevatedButton(
            onPressed: uploadImage,
            child: const Text('Upload Image'),
        ),

            ...

The build method is used to create the UI of the ProfilePage class. The UI consists of a Scaffold widget that contains
two ElevatedButton widgets. The first button is used to allow the user to select an image from their device. The second
button is used to upload the selected image to Firebase Storage.

.. code-block::

         Row(
            children: [
                Container(

                    ...
                    
                    child: TextFormField(
                        initialValue: fName,
                        decoration: const InputDecoration(
                            labelText: 'First Name',
                            border: OutlineInputBorder(),
                        ),
                        onChanged: (value) {
                            setState(() {
                            fName = value;

                    ...

                    child: TextFormField(
                        initialValue: lName,
                        decoration: const InputDecoration(
                            labelText: 'Last Name',
                            border: OutlineInputBorder(),
                        ),
                        onChanged: (value) {
                            setState(() {
                            lName = value;
                            });

                    ...

After the buttons, the UI contains two TextFormField widgets. These widgets are used to allow the user to input their
first name and last name. When these are changed, the local values of lName and fName are updated.

.. code-block::

    child: TextFormField(
        initialValue: email,
        decoration: const InputDecoration(
            labelText: 'E-Mail Address',
            border: OutlineInputBorder(),
        ),
        onChanged: (value) {
            setState(() {
                email = value;

        ...

    child: TextFormField(
        initialValue: phoneNumber,
        decoration: const InputDecoration(
            labelText: 'Phone Number (Optional)',
            border: OutlineInputBorder(),
        ),
        keyboardType: TextInputType.number,
        onChanged: (value) {
            setState(() {
            phoneNumber = value;

        ...

    child: TextFormField(
        initialValue: skills,
        minLines: 1,
        maxLines: 6,
        decoration: const InputDecoration(
            labelText: 'Skills',
            border: OutlineInputBorder(),
        ),
        onChanged: (value) {
            setState(() {
            skills = value;

        ...

The UI also contains three more TextFormField widgets. These widgets are used to allow the user to input their email,
phone number, and skills. Like the other TextFormField widgets, when these are changed, the local values of email, 
phoneNumber, and skills are updated.

.. code-block::

    Text(
        errMessage,
        style: const TextStyle(color: Colors.red, fontSize: 16),
        ),

The UI also contains a Text widget that displays an error message if any of the user's inputs are invalid. The error
message is initially set to null. During the checkInputs method, if any of the user's inputs are invalid, the error 
message is updated accordingly

.. code-block::

    ElevatedButton(
        child: const Text('Save Changes'),
        onPressed: () {
            checkInputs(fName, lName, email, phoneNumber, skills);
        },
    ),

At the bottom of the profile page UI, there is an ElevatedButton widget. This button is used to allow the user to save 
all of their changes. The button takes in the values of the TextFormFields and calls the checkInputs method to validate 
them all.