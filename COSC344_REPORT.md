# COSC344 Assignment 1 Report

Leader: Hayden McAlister

Members: Masaaki Fukushima, Jack Heikell, Nat Moore

## Miniworld Description

For this assignment we have chosen to model a university as our miniworld. Technically, we are modelling the University of Otago, but we have added enough abstraction that this could be considered as any university. We have chosen this miniworld as it is one that we have plenty of experience with, and thus we can accurately model it, as well as appreciate the complexities in entity types and relationship types. We have determined that our model assumes the following:

- A university is an academic institution with staff and students, which are associated with some courses.
- A university exists over multiple campuses, each campus with many buildings, each building having many rooms, each room serving many purposes.
- A university has many departments, each department offering many courses, and each department offering many papers.
- A course is associated with some papers, which are required to complete a course.

Using these assumptions, we can easily see that this miniworld has enough complexity in terms of entity types and relationship types to meet the goals of this assignment.

## Entity Types and Attributes
- ### Student
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Student_ID | Simple, Not NULL | Single-valued | Int (key Attribute) |
| Name | Simple, Not NULL | Single-valued | String |
| Phone | Simple | Single-valued | String |
| Address | Composite<br>(Street Number: int[1,10000],<br>Street Name: Str, Suburb: Str) | Multi-valued | String |
| Course | Simple | Multi-valued | String | TODO: By reference?
| Enrollment_Date | Simple, Not NULL | Single-valued | Date |
| Graduate_Date | Simple | Single-valued | Date |
| Graduated_bool | Derived<br>(From existence of Graduate_Date) | Single-valued | Boolean |

- ### Staff
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Staff_ID | Simple, Not NULL | Single-valued | Int (Key Attribute) |
| Name | Simple, Not NULL | Single-valued | String |
| Phone | Simple | Single-valued | String |
| Address | Composite<br>(Street Number: int[1,10000],<br>Street Name: Str, Suburb: Str) | Multi-valued | String |
| Salary | Simple, Not NULL | Single-valued | Float |
| IRD_Num | Simple, not NULL | Single-valued | Int |

- ### Department
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Name | Simple | Single-valued | String (Key Attribute)|
| Campus | Simple | Derived<br>(From Campus Reference) | String |
| Number_of_Employees | Composite<br>(Number_of_academic_staff: int,<br> Number_of_nonacademic_staff: int) | Single-valued | int |
| Number_of_Students | Derived<br>(from Student references) | Single-valued | int |
| Address | Derived<br>(from Building references) | Multi-valued | String |

- ### Course
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Name | Simple | Single-valued | String (Key Attribute) |
| Years_required | Simple | Single-valued | int |
| Undergraduate | Simple | Single-valued | boolean |
| Postgraduate | Simple | Single-valued | boolean |
| Number_of_Students | Derived<br>(from Student references) | Single-valued | int |

- ### Paper
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Paper_Code | Simple | Single-valued | String (Key Attribute) |
| Semester | Simple | Multi-valued | String (Enumerated) |
| Points | Simple | Single-valued | Int |  

- ### Campus
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Name | Single-valued | Single-valued | String (Key Attribute) |
| Main_Office_Address | Single-valued | Single-valued | String |
| Phone | Simple | Single-valued | String (Candidate Key) |
| Email | Simple | Single-valued | String (Candidate Key) |

- ### Building
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Address | Composite<br>(Street Number: int[1,10000],<br>Street Name: Str, Suburb: Str) | Single-valued | String (Key Attribute) |
| Post code | Simple | Single-valued | Int (Four Digit) |
| Name | Simple | Single-valued | String |

- ### Room
| Attribute   | Simplicity  | Num-Values  | Data Type   |
| ----------- | ----------- | ----------- | ----------- |
| Room Number | Simple | Single-valued | Int, [1,10000] (Partial Key)|
| Seating | Simple | Single-valued | Int [1,10000] |
| Accessibility | Simple | Single-valued | Boolean |
| Projector | Simple | Single-valued | Boolean |

## Relationship Types and Attributes
- ### LOCATED_ON (Building, Campus)
  - N:1 for Building:Campus
  - A building can only be on one campus, but one campus can have many buildings
  - Building has total participation, Campus has partial participation
  - A building must exist on a campus, but a campus doesn't need to have buildings
  
- ### BASED_IN (Department, Building)
  - N:M for Department:Building
  - A building can have many departments, and a department can be based in many buildings
  - Department has total participation, Building has partial participation
  - A department must have a building associated with it, a building does not need a department

- ### LOCATED_IN (Room, Building)
  - N:1 for Room:Building
  - A building can have many rooms, but a room cannot exist in many buildings
  - Room has total participation, Building has partial participation
  - A room cannot exist without a building, but a building may have no rooms

- ### OFFICE_OF (Room, Staff)
  - 1:N for Room:Staff
  - Many staff may share a room, but a staff cannot have multiple rooms
  - Room is partial participation, staff is partial participation
  - A room does not need to be an office, and a staff does not need to have an office

- ### LECTURED_IN(Paper, Room)
  - M:N for Paper:Room
  - A paper may be lectured in multiple rooms, while a room may be the lecture hall for many papers
  - Room is partial participation, paper is partial participation
  - A room does not need to be a lecture hall, and a paper does not need to have lectures

- ### WORKS_FOR(Staff, Department)
  - M:N relationship for Staff:Department
  - A staff member can work for multiple departments (e.g., a tutor who works for both the Computer Science and Information Science departments), and a department can have multiple staff members
  - STAFF is total participation and DEPARTMENT is total participation; every staff member must work for at least one department, and every department must have  ≥ 1 staff member

- ### OFFERS (Department, Paper)
  - 1:N for Department:Paper
  - A department can offer multiple papers, but a paper can only be coordinated by one department
  - DEPARTMENT is total participation, and so too is PAPER; a department must offer papers, and a paper can only be offered by a department

- ### ENROLLED_IN (Student, Course)
  - 2:N for Student:Course
  - A student can enrol in up to two courses simultaneously, and a course can have multiple students
  - STUDENT has total participation; COURSE has partial participation
  - A student must be enrolled in a course, but an unpopular or poorly marketed course might not have any students

- ### COUNTS_TOWARD(Paper, Course)
  - M:N relationship for Paper:Course
  - A paper might count toward multiple courses (e.g., one student might take STAT110 as part of their BA in psychology, while another might take STAT110 as part of their BAppSc in Data Science), and a course might have many approved papers
  - PAPER is total participation; COURSE is total participation. 
  - A paper must count toward a course (otherwise, only students with a cavalier disregard for their student loans would enrol), and a course must have papers that students can take to fulfill the course requirements (otherwise, students could get a qualification without having done any work)

- ### POSSIBLE_MAJOR_FOR(Department, Course)
  - M:N relationship for Department:Course
  - A department might represent a possible major for several courses (e.g., computer science can be a major for a student studying either a BSc or a BA), and a course might have dozens of affiliated departments that represent possible majors (see [here](https://www.otago.ac.nz/courses/qualifications/bsc.html#majorsubjects))
  - DEPARTMENT is partial participation and COURSE is partial participation
  - A department may not represent a possible major for any course, and a course might not require its students to major in a subject

- ### SUPERVISES(Staff, Student)
  - M:N for Staff:Student
  - Staff can supervise many students, and students can have multiple
    supervisors.
  - Both are partial
  - Staff don’t always supervise students , and not all students need supervisors.

- ### COORDINATES(Staff, Course)
  - 1:1 for Staff:Course
  - A course must have one coordinator and staff can coordinate only one course
  - Staff has partial participation, course has total participation

- ### TEACHES (Staff, Paper)
  - N:M for Staff:Paper
  - Many staff members can teach one paper, and one staff member can teach many papers
  - Staff is partial, Paper is total

- ### OFFERED_AT (Paper, Campus)
  - N:M for Paper:Campus
  - A campus can offer many papers, and a paper can be offered at many campuses
  - Paper and Campus are total, a campus must offer a paper and a paper must be offered at a campus

- ### TAKES (Student, Paper)
  - N:M for Student:Paper
  - A student can take many papers, and a paper can be taken by many students
  - Student is total participation, paper is partial participation
  - An unpopular paper may have no students

- ### DEAN_OF (Staff, Campus)
  - 1:1 for Staff:Campus
  - A campus can have one dean, and a staff member can only be the dean on one campus
  - Total participation for campus, partial participation for staff
  - A campus needs a dean but a staff member doesn't need to be the dean

- ### STUDENT_AT (Student, Campus)
  - 1:N For Campus:Student, a campus can have many students but a student can only be enrolled at one campus
  - Student has total participation (they must be at a campus) and campus has partial participation (a campus may have no students)

- ### STAFF_AT (Staff, Campus)
  - 1:N For Campus:Staff, a campus can have many staff but a staff member can only be at one campus
  - Staff has total participation (they must be at a campus) and campus has total participation (it must be staffed by someone)

## Teamwork Summary
- ### Hayden McAlister
  - Modeling of Building and Room, as well as associated Relationships
  - Structuring of Report and DIA file
  - Writeup of Building, Room, and associated relationships in report

- ### Nat Moore
  - Modeling of Department, Course, and associated relationships
  - Addition of above working to report

- ### Masaaki Fukushima
  - Modeling of Paper, Campus and associated relationships
  - Addition of above working to report

- ### Jack Heikell 
  - Modeling of Student, Staff, and associated relationships
  - Addition of above working to report
