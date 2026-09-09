Laboratory Work 1: Relational Model, Keys, ER Modeling & Normalization

Part 1: Key Identification Exercises

Task 1.1: Superkey and Candidate Key Analysis

Relation A: Employee
`Employee (EmpID, SSN, Email, Phone, Name, Department, Salary)`

1. List at least 6 different superkeys:
1. `{EmpID}`
2. `{SSN}`
3. `{Email}`
4. `{EmpID, Name}`
5. `{SSN, Email}`
6. `{EmpID, SSN, Email, Phone, Name, Department, Salary}`

2. Identify all candidate keys:
`{EmpID}`
`{SSN}`
`{Email}`

(Note: Each of these attributes uniquely identifies a tuple and is minimal—removing any attribute from them makes them non-unique).

3. Which candidate key would you choose as primary key and why?
4. Chosen Primary Key: `EmpID`
Reasoning: 
`EmpID` is a short, unique integer surrogate key, which makes indexing and join operations highly performant.
`SSN` contains sensitive personal information (PII) and should not be widely exposed across foreign key references for security/privacy reasons.
`Email` is a string (takes more storage/index memory) and can potentially change if an employee changes their legal name or department alias.

4. Can two employees have the same phone number? Justify your answer based on the data shown.
Yes, based strictly on the current relation structure, `Phone` is **not** identified as a candidate key.
Justification from data: In many organizations, employees may share a corporate landline, front-desk extension, or department contact number. Unless explicit business rules enforce unique phone numbers, `Phone` remains a non-key attribute.

---

Relation B: Course Registration
`Registration (StudentID, CourseCode, Section, Semester, Year, Grade, Credits)`

Business Rules:
1. A student can take the same course in different semesters.
2. A student cannot register for the same course section in the same semester.
3. Each course section in a semester has a fixed credit value.

1. Determine the minimum attributes needed for the primary key:
Primary Key:`{StudentID, CourseCode, Section, Semester, Year}`

2. Explain why each attribute in your primary key is necessary:
`StudentID`: Identifies *who* is taking the course.
`CourseCode`: Identifies *which course* is being taken.
`Section`: Differentiates between multiple sections of the same course offering (e.g., Section 1 vs. Section 2).
`Semester`: Identifies the term (e.g., Fall, Spring).
`Year`: Differentiates course offerings across different academic years.
Removal test: Removing `Section` would allow a student to register for two different sections in the same term; removing `Semester` or `Year` would violate Rule 1 (taking the course again in another term).

3. Identify any additional candidate keys (if they exist):
No additional candidate keys exist for this relation under the provided business rules.

---

Task 1.2: Foreign Key Design

Given Tables:
`Student (StudentID, Name, Email, Major, AdvisorID)`
`Professor (ProfID, Name, Department, Salary)`
`Course (CourseID, Title, Credits, DepartmentCode)`
`Department (DeptCode, DeptName, Budget, ChairID)`
`Enrollment (StudentID, CourseID, Semester, Grade)`

Identified Foreign Key Relationships:
1. `Student(AdvisorID)` -> references `Professor(ProfID)`
2. `Course(DepartmentCode)` -> references `Department(DeptCode)`
3. `Department(ChairID)` -> references `Professor(ProfID)`
4. `Enrollment(StudentID)` -> references `Student(StudentID)`
5. `Enrollment(CourseID)` -> references `Course(CourseID)`

---

Part 2: ER Diagram Construction

Task 2.1: Hospital Management System

1. Entities & Classification:
Strong Entities:
`Patient`
`Doctor`
`Department`
Weak Entity:
`Room` (Depends on `Department` because room numbers are only unique within a specific department, e.g., Room 101 in Cardiology).

2. Attributes Classification:
Patient:
`PatientID` (Simple, Primary Key)
`Name` (Simple/Composite)
`Birthdate` (Simple)
`Address` (Composite: `Street`, `City`, `State`, `Zip`)
`Phone` (Multi-valued)
`InsuranceInfo` (Simple)
Doctor:
`DoctorID` (Simple, Primary Key)
`Name` (Simple)
`Specialization` (Multi-valued)
`Phone` (Simple)
`OfficeLocation` (Simple)
Department:
`DeptCode` (Simple, Primary Key)
`DeptName` (Simple)
`Location` (Simple)
Room (Weak Entity):
`RoomNumber` (Partial Key)
Appointment (Associative Entity / Relationship Attributes):
`DateTime` (Simple)
`Purpose` (Simple)
`Notes` (Simple)
Prescription (Associative Entity / Relationship Attributes):
`Dosage` (Simple)
`Instructions` (Simple)

3. Relationships & Cardinalities:
Patient - Sees - Doctor (via Appointment): `M:N`
Doctor - Prescribes - Patient (via Prescription): `M:N`
Doctor - Works_In - Department: `N:1` (Many Doctors work in 1 Department)
Department - Has - Room: `1:N` (Identifying Relationship for Weak Entity `Room`)

Diagram:
![Hospital ERD](diagrams/task2_1_hospital.png)

---

Task 2.2: E-commerce Platform

1. ER Structural Components:
Entities: `Customer`, `Order`, `Product`, `Category`, `Vendor`, `Review`.
Weak Entity: `OrderItem` (Weak entity dependent on `Order`; identified by `OrderNo` + `ItemSeq`).

2. Weak Entity Justification:
3. `OrderItem` cannot exist independently without a parent `Order`. It uses a partial key (like `LineNumber`) alongside the `OrderID` foreign/owner key to form its identity.

3. Many-to-Many Relationship with Attributes:
Customer - Rates/Reviews - Product:
Relationship attributes: `Rating`, `ReviewText`, `ReviewDate`.
Order - Contains - Product (resolved via `OrderItem`):
Attributes on relationship/weak entity: `Quantity`, `UnitPriceAtPurchase`.

Diagram:
![E-commerce ERD](diagrams/task2_2_ecommerce.png)

---

Part 4: Normalization Workshop

Task 4.1: Denormalized Table Analysis

`StudentProject (StudentID, StudentName, StudentMajor, ProjectID, ProjectTitle, ProjectType, SupervisorID, SupervisorName, SupervisorDept, Role, HoursWorked, StartDate, EndDate)`

1. Functional Dependencies (FDs):
FD1: `StudentID` -> `StudentName`, `StudentMajor`
FD2: `ProjectID` -> `ProjectTitle`, `ProjectType`, `SupervisorID`
FD3: `SupervisorID` -> `SupervisorName`, `SupervisorDept`
FD4: `StudentID`, `ProjectID` -> `Role`, `HoursWorked`, `StartDate`, `EndDate`

2. Problems & Anomalies:
Redundancy: `StudentName`, `ProjectTitle`, and `SupervisorName` are duplicated across every project assignment record.
Update Anomaly: Changing a supervisor's department requires updating multiple rows across all projects supervised.
Insert Anomaly: Cannot register a new supervisor or student until they are assigned to an active project.
Delete Anomaly: Deleting a project record might remove the only record of a student or supervisor in the system.

3. 1NF Analysis:
Violations: Assuming attributes are atomic, it is in 1NF. If `Role` or `Supervisor` contained comma-separated lists, it would violate 1NF.
Fix: Ensure all attributes hold atomic values.

4. 2NF Decomposition:
5. Primary Key: `(StudentID, ProjectID)`
Partial Dependencies: FD1 and FD2 depend only on part of the composite key (`StudentID` or `ProjectID`).
2NF Tables:
`Student (StudentID [PK], StudentName, StudentMajor)`
`Project (ProjectID [PK], ProjectTitle, ProjectType, SupervisorID, SupervisorName, SupervisorDept)`
`ProjectAssignment (StudentID [PK*], ProjectID [PK*], Role, HoursWorked, StartDate, EndDate)`

5. 3NF Decomposition:
Transitive Dependency: In table `Project`, `ProjectID` -> `SupervisorID` and `SupervisorID` -> `SupervisorName`, `SupervisorDept` (FD3).
Final 3NF Schemas:
`Student (StudentID [PK], StudentName, StudentMajor)`
`Supervisor (SupervisorID [PK], SupervisorName, SupervisorDept)`
`Project (ProjectID [PK], ProjectTitle, ProjectType, SupervisorID [FK])`
`ProjectAssignment (StudentID [PK*, FK], ProjectID [PK*, FK], Role, HoursWorked, StartDate, EndDate)`

---

Task 4.2: Advanced Normalization

`CourseSchedule (StudentID, StudentMajor, CourseID, CourseName, InstructorID, InstructorName, TimeSlot, Room, Building)`

1. Primary Key:
Primary Key: `(StudentID, CourseID, TimeSlot)`
(Alternatively, `(StudentID, Room, TimeSlot)` since `Room` + `TimeSlot` uniquely identifies a section offering).

2. Functional Dependencies (FDs):
FD1: `StudentID` -> `StudentMajor`
FD2: `CourseID` -> `CourseName`
FD3: `InstructorID` -> `InstructorName`
FD4: `Room` -> `Building`
FD5: `CourseID`, `TimeSlot`, `Room` -> `InstructorID`
FD6: `InstructorID`, `TimeSlot` -> `Room`

3. BCNF Analysis:
Check: A table is in BCNF if for every non-trivial functional dependency X -> Y, X is a superkey.
Result:Not in BCNF. Determinants like `StudentID`, `CourseID`, `InstructorID`, and `Room` are not superkeys.

4. BCNF Decomposition Steps:
1. Decompose by FD1:
`Student (StudentID [PK], StudentMajor)`
2. Decompose by FD2:
3. `Course (CourseID [PK], CourseName)`
4. Decompose by FD3:
`Instructor (InstructorID [PK], InstructorName)`
5. Decompose by FD4:
`Location (Room [PK], Building)`
6. Decompose Section Schedule:
`Section (CourseID [FK], TimeSlot, Room [FK], InstructorID [FK])`
7. Decompose Student Enrollment:
`Enrollment (StudentID [FK], CourseID [FK], TimeSlot)`

5. Loss of Information / Dependency Preservation:
All functional dependencies are preserved across decomposed relations without loss of information under natural join operations.

---

Part 5: Design Challenge

Task 5.1: Real-World Application (Student Clubs)

1 & 2. Normalized Relational Schema

`Student (StudentID [PK], Name, Email, Major)`
`FacultyAdvisor (AdvisorID [PK], Name, Email, Department)`
`Club (ClubID [PK], ClubName, Description, AdvisorID [FK])`
`ClubMember (StudentID [PK*, FK], ClubID [PK*, FK], JoinDate)`
`ClubOfficer (StudentID [PK*, FK], ClubID [PK*, FK], Role [PK], Term)`
`Room (RoomID [PK], Building, RoomNumber, Capacity)`
`Event (EventID [PK], ClubID [FK], EventName, Date, StartTime, EndTime, RoomID [FK])`
`EventAttendance (StudentID [PK*, FK], EventID [PK*, FK], CheckInTime)`
`BudgetTransaction (TransactionID [PK], ClubID [FK], Amount, Type, Date, Description)`

Diagram:
![Student Clubs ERD](diagrams/task5_1_student_clubs.png)

---

3. Design Decision & Justification:
Option A: Store `OfficerRole` as a column inside `ClubMember`.
Option B (Chosen): Create a separate `ClubOfficer` relation.
Reasoning: Option B allows tracking historical leadership roles over different academic years/terms and handles cases where a student holds distinct positions across multiple clubs simultaneously without duplication or multi-valued attribute violations.

---

4. 3 Example Queries (English):
1. "Find all students who hold officer positions in the Computer Science Club during the 2026 academic year."
2. "List all club events scheduled for next week along with their assigned room number and building location."
3. "Calculate the total remaining budget balance for each club by summing income and subtracting expenses."
