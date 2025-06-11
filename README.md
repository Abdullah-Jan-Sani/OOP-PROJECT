# Case Study: University Management System (UMS)

##  Objective:
Design a simple University Management System using Object-Oriented Programming in C++. This system will manage:

- Students
- Faculty
- Courses
- Departments

Students should apply OOP principles: **Encapsulation, Inheritance, Polymorphism, and Abstraction**.

---

##  Problem Statement:
You are hired to develop a simple University Management System. Your system must:

- Register students and faculty members with relevant details.
- Assign courses to students and faculty.
- Display details of students, faculty, and the courses they are involved in.
- Use inheritance to avoid repetition.
- Use polymorphism to display info in a dynamic way.

---

##  Requirements:

###  Base Class: Person
- **Data Members:** name, age, CNIC  
- **Member Functions:** getData(), displayData()

###  Derived Class: Faculty (from Person)
- **Data Members:** facultyId, designation, courseAssigned  
- **Functions:** override displayData() to show faculty-specific info

###  Derived Class: Student (from Person)
- **Data Members:** rollNo, degreeProgram, registeredCourses[]  
- **Functions:** override displayData() to show student-specific info

###  Class: Course
- **Data Members:** courseCode, courseName, creditHours

Use arrays to handle multiple students and faculty. Demonstrate the use of:
- Polymorphism using virtual functions
- Encapsulation by using private members and public accessors
- Inheritance between base and derived classes
