Step 1: Open Setup

Top-right:
⚙️ → Setup


Step 2: Create Object

Go to:

Setup → Object Manager → Create → Custom Object



Step 3:Create Fields in Student Object

Now create fields like database columns.

Inside:
Object Manager → Student

Click:
Fields & Relationships → New



Step 4:Open Developer Console

Top-right:
⚙️ → Developer Console

This is where you write Apex code.



Step 5:Test the Program

Open:
Developer Console → Debug → Open Execute Anonymous Window



Step 6:View Student Records in UI

Go to:

App Launcher → Students

You can see all records visually.
If “Students” Is NOT Visible

This happens sometimes because Salesforce has not created a tab automatically.

Then do this:

Create Students Tab

Go to:

Setup
→ Tabs

Search:

Tabs
Under Custom Object Tabs

Click:

New
Select Object

Choose:

Student

Click:
Next → Next → Save

Now Open App Launcher Again

Search:

Students

Now it will appear.

Alternative Direct Method

Go to:

Setup
→ Object Manager
→ Student
→ View Object

Then click:

Records

to see all stored students.




Step 7:Create Visualforce Page (Optional but Recommended)

Go to:
Setup → Visualforce Pages → New

Name:
StudentMenu

----------------------------------------------------------------------------------------

StudentManagementSystem

public class StudentManagementSystem {

    // Add Student
    public static void addStudent(
        String name,
        Integer roll,
        Integer age,
        String dept,
        Decimal marks
    ) {

        Student__c s = new Student__c();

        s.Name = name;
        s.Roll_no__c = roll;
        s.Age__c = age;
        s.Department__c = dept;
        s.Marks__c = marks;

        insert s;
    }


    // Display All Students
    public static List<Student__c> displayStudents() {

        return [

            SELECT Name,
                   Roll_no__c,
                   Age__c,
                   Department__c,
                   Marks__c

            FROM Student__c
        ];
    }


    // Search Student
    public static List<Student__c> searchStudent(
        Integer roll
    ) {

        return [

            SELECT Name,
                   Roll_no__c,
                   Age__c,
                   Department__c,
                   Marks__c

            FROM Student__c

            WHERE Roll_no__c = :roll
        ];
    }


    // Update Marks
    public static void updateMarks(
        Integer roll,
        Decimal newMarks
    ) {

        Student__c s = [

            SELECT Id,
                   Marks__c

            FROM Student__c

            WHERE Roll_no__c = :roll

            LIMIT 1
        ];

        s.Marks__c = newMarks;

        update s;
    }


    // Delete Student
    public static void deleteStudent(
        Integer roll
    ) {

        Student__c s = [

            SELECT Id

            FROM Student__c

            WHERE Roll_no__c = :roll

            LIMIT 1
        ];

        delete s;
    }
}

-------------------------------------------------------------------------------------------

StudentController


public class StudentController {

    // Form Fields
    public String name {get; set;}

    public Integer roll {get; set;}

    public Integer age {get; set;}

    public String dept {get; set;}

    public Decimal marks {get; set;}


    // Dropdown Action
    public String selectedAction {get; set;}


    // Student List
    public List<Student__c> studentList {get; set;}


    // Constructor
    public StudentController() {

        selectedAction = 'Add';

        studentList = new List<Student__c>();
    }


    // Execute Button
    public void executeAction() {

        if(selectedAction == 'Add') {

            addStudent();
        }

        else if(selectedAction == 'Search') {

            searchStudent();
        }

        else if(selectedAction == 'Update') {

            updateMarks();
        }

        else if(selectedAction == 'Delete') {

            deleteStudent();
        }

        else if(selectedAction == 'Display') {

            displayStudents();
        }
    }


    // Add Student
    public void addStudent() {

        StudentManagementSystem.addStudent(
            name,
            roll,
            age,
            dept,
            marks
        );

        // LOG MESSAGE
        System.debug('Student Added Successfully');

        // UI MESSAGE
        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Student Added Successfully'
            )
        );

        clearFields();
    }


    // Display Students
    public void displayStudents() {

        studentList =
            StudentManagementSystem.displayStudents();

        System.debug('Displaying All Students');

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Student Records Displayed'
            )
        );

        clearFields();
    }


    // Search Student
    public void searchStudent() {

        studentList =
            StudentManagementSystem.searchStudent(
                roll
            );

        System.debug(
            'Search Completed For Roll No: ' + roll
        );

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Student Search Completed'
            )
        );

        Integer tempRoll = roll;

        clearFields();

        roll = tempRoll;
    }


    // Update Marks
    public void updateMarks() {

        StudentManagementSystem.updateMarks(
            roll,
            marks
        );

        System.debug(
            'Marks Updated For Roll No: ' + roll
        );

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Marks Updated Successfully'
            )
        );

        clearFields();
    }


    // Delete Student
    public void deleteStudent() {

        StudentManagementSystem.deleteStudent(
            roll
        );

        System.debug(
            'Student Deleted With Roll No: ' + roll
        );

        ApexPages.addMessage(
            new ApexPages.Message(
                ApexPages.Severity.CONFIRM,
                'Student Deleted Successfully'
            )
        );

        clearFields();
    }


    // Clear Fields
    public void clearFields() {

        name = '';

        roll = null;

        age = null;

        dept = '';

        marks = null;
    }
}


--------------------------------------------------------------------------------------------------


visual force

<apex:page controller="StudentController">

    <apex:form >

        <apex:pageMessages id="msg"/>

        <!-- MAIN BLOCK -->

        <apex:pageBlock title="Student Management System"
            id="mainBlock">

            <!-- DROPDOWN MENU -->

            <apex:pageBlockSection columns="1">

                <apex:selectList value="{!selectedAction}"
                    size="1">

                    <apex:actionSupport event="onchange"
                        reRender="mainBlock"/>

                    <apex:selectOption itemValue="Add"
                        itemLabel="Add Student"/>

                    <apex:selectOption itemValue="Search"
                        itemLabel="Search Student"/>

                    <apex:selectOption itemValue="Update"
                        itemLabel="Update Marks"/>

                    <apex:selectOption itemValue="Delete"
                        itemLabel="Delete Student"/>

                    <apex:selectOption itemValue="Display"
                        itemLabel="Display Students"/>

                </apex:selectList>

            </apex:pageBlockSection>


            <!-- ADD STUDENT -->

            <apex:pageBlockSection columns="2"
                rendered="{!selectedAction == 'Add'}">

                <apex:inputText value="{!name}"
                    label="Name"/>

                <apex:inputText value="{!roll}"
                    label="Roll No"/>

                <apex:inputText value="{!age}"
                    label="Age"/>

                <apex:inputText value="{!dept}"
                    label="Department"/>

                <apex:inputText value="{!marks}"
                    label="Marks"/>

            </apex:pageBlockSection>


            <!-- SEARCH STUDENT -->

            <apex:pageBlockSection columns="1"
                rendered="{!selectedAction == 'Search'}">

                <apex:inputText value="{!roll}"
                    label="Enter Roll No"/>

            </apex:pageBlockSection>


            <!-- UPDATE MARKS -->

            <apex:pageBlockSection columns="2"
                rendered="{!selectedAction == 'Update'}">

                <apex:inputText value="{!roll}"
                    label="Roll No"/>

                <apex:inputText value="{!marks}"
                    label="New Marks"/>

            </apex:pageBlockSection>


            <!-- DELETE STUDENT -->

            <apex:pageBlockSection columns="1"
                rendered="{!selectedAction == 'Delete'}">

                <apex:inputText value="{!roll}"
                    label="Roll No"/>

            </apex:pageBlockSection>


            <!-- DISPLAY -->

            <apex:pageBlockSection columns="1"
                rendered="{!selectedAction == 'Display'}">

                <apex:outputText value="Click Execute to display all students."/>

            </apex:pageBlockSection>


            <!-- EXECUTE BUTTON -->

            <apex:pageBlockButtons >

                <apex:commandButton value="Execute"
                    action="{!executeAction}"
                    reRender="table,mainBlock,msg"/>

            </apex:pageBlockButtons>

        </apex:pageBlock>


        <!-- OUTPUT TABLE -->

        <apex:pageBlock title="Student Records"
            id="table">

            <apex:pageBlockTable value="{!studentList}"
                var="s">

                <apex:column value="{!s.Name}"
                    headerValue="Name"/>

                <apex:column value="{!s.Roll_no__c}"
                    headerValue="Roll No"/>

                <apex:column value="{!s.Age__c}"
                    headerValue="Age"/>

                <apex:column value="{!s.Department__c}"
                    headerValue="Department"/>

                <apex:column value="{!s.Marks__c}"
                    headerValue="Marks"/>

            </apex:pageBlockTable>

        </apex:pageBlock>

    </apex:form>

</apex:page>



