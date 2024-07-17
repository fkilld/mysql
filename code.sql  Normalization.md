 Let's illustrate the example with code in MySQL to show the difference between the non-normalized table and the normalized tables according to 1NF.

### Initial Non-Normalized Table

Let's start with the initial table that violates 1NF:

```sql
CREATE TABLE NonNormalizedSchool (
    StudentID INT,
    StudentName VARCHAR(50),
    Subjects VARCHAR(100),
    Teachers VARCHAR(100)
);

INSERT INTO NonNormalizedSchool (StudentID, StudentName, Subjects, Teachers)
VALUES
(1, 'Alice Smith', 'Math, English', 'Mr. Brown, Ms. Green'),
(2, 'Bob Johnson', 'Science, History, Art', 'Mr. White, Mr. Black, Ms. Blue');
```

### Problems with the Initial Table

1. **Non-Atomic Values**: The `Subjects` and `Teachers` columns contain multiple values in a single cell.
2. **Difficult to Query**: It's challenging to find students based on specific subjects or teachers.
3. **Redundancy and Anomalies**: Adding or removing subjects or teachers can lead to inconsistencies and redundancy.

### Converting to 1NF

To comply with 1NF, we'll break down the `Subjects` and `Teachers` columns into separate tables.

#### Creating Normalized Tables

**Students Table:**

```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    StudentName VARCHAR(50)
);

INSERT INTO Students (StudentID, StudentName)
VALUES
(1, 'Alice Smith'),
(2, 'Bob Johnson');
```

**Subjects Table:**

```sql
CREATE TABLE Subjects (
    StudentID INT,
    Subject VARCHAR(50),
    PRIMARY KEY (StudentID, Subject),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
);

INSERT INTO Subjects (StudentID, Subject)
VALUES
(1, 'Math'),
(1, 'English'),
(2, 'Science'),
(2, 'History'),
(2, 'Art');
```

**Teachers Table:**

```sql
CREATE TABLE Teachers (
    StudentID INT,
    Teacher VARCHAR(50),
    PRIMARY KEY (StudentID, Teacher),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
);

INSERT INTO Teachers (StudentID, Teacher)
VALUES
(1, 'Mr. Brown'),
(1, 'Ms. Green'),
(2, 'Mr. White'),
(2, 'Mr. Black'),
(2, 'Ms. Blue');
```

#### Benefits of 1NF

1. **Atomic Values**: Each column contains only atomic values.
2. **Easy to Query**: Simplifies queries to find students based on subjects or teachers.
3. **Eliminates Redundancy**: Minimizes data redundancy.
4. **Prevents Anomalies**: Reduces insertion, update, and deletion anomalies.

### Summary

By converting the non-normalized table into normalized tables according to 1NF, we ensure that each cell contains only a single value, and each record is unique. This makes the data more organized, easier to query, and free from redundancy and anomalies.

Now, the normalized structure is:

- **Students**: Stores unique student information.
- **Subjects**: Stores student-subject relationships.
- **Teachers**: Stores student-teacher relationships.

This setup adheres to the principles of 1NF and enhances the overall database design.




#
#
 let's move forward with coding the Second Normal Form (2NF) transformation of our school database example.

### Initial Setup in 1NF

We start with the 1NF tables as previously established:

#### Students Table:

```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    StudentName VARCHAR(50)
);

INSERT INTO Students (StudentID, StudentName)
VALUES
(1, 'Alice Smith'),
(2, 'Bob Johnson');
```

#### Subjects Table:

```sql
CREATE TABLE Subjects (
    StudentID INT,
    Subject VARCHAR(50),
    Classroom VARCHAR(50),
    PRIMARY KEY (StudentID, Subject),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
);

INSERT INTO Subjects (StudentID, Subject, Classroom)
VALUES
(1, 'Math', 'A101'),
(1, 'English', 'B202'),
(2, 'Science', 'C303'),
(2, 'History', 'D404'),
(2, 'Art', 'E505');
```

#### Teachers Table:

```sql
CREATE TABLE Teachers (
    StudentID INT,
    Teacher VARCHAR(50),
    PRIMARY KEY (StudentID, Teacher),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
);

INSERT INTO Teachers (StudentID, Teacher)
VALUES
(1, 'Mr. Brown'),
(1, 'Ms. Green'),
(2, 'Mr. White'),
(2, 'Mr. Black'),
(2, 'Ms. Blue');
```

### Converting to 2NF

To ensure 2NF, we need to remove partial dependencies. This involves creating separate tables for subjects and teachers, ensuring that non-key attributes are fully dependent on the entire primary key.

#### Creating Normalized Tables in 2NF

1. **Create a separate `StudentSubjects` table:**

```sql
CREATE TABLE StudentSubjects (
    StudentID INT,
    SubjectID INT,
    PRIMARY KEY (StudentID, SubjectID),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
    FOREIGN KEY (SubjectID) REFERENCES Subjects(SubjectID)
);

INSERT INTO StudentSubjects (StudentID, SubjectID)
VALUES
(1, 101),
(1, 102),
(2, 103),
(2, 104),
(2, 105);
```

2. **Create a `Subjects` table with subject and classroom details:**

```sql
CREATE TABLE Subjects (
    SubjectID INT PRIMARY KEY,
    SubjectName VARCHAR(50),
    Classroom VARCHAR(50)
);

INSERT INTO Subjects (SubjectID, SubjectName, Classroom)
VALUES
(101, 'Math', 'A101'),
(102, 'English', 'B202'),
(103, 'Science', 'C303'),
(104, 'History', 'D404'),
(105, 'Art', 'E505');
```

3. **Create a separate `StudentTeachers` table:**

```sql
CREATE TABLE StudentTeachers (
    StudentID INT,
    TeacherID INT,
    PRIMARY KEY (StudentID, TeacherID),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
    FOREIGN KEY (TeacherID) REFERENCES Teachers(TeacherID)
);

INSERT INTO StudentTeachers (StudentID, TeacherID)
VALUES
(1, 201),
(1, 202),
(2, 203),
(2, 204),
(2, 205);
```

4. **Create a `Teachers` table with teacher details:**

```sql
CREATE TABLE Teachers (
    TeacherID INT PRIMARY KEY,
    TeacherName VARCHAR(50)
);

INSERT INTO Teachers (TeacherID, TeacherName)
VALUES
(201, 'Mr. Brown'),
(202, 'Ms. Green'),
(203, 'Mr. White'),
(204, 'Mr. Black'),
(205, 'Ms. Blue');
```

### Summary

By converting our database to 2NF, we have:

- Removed partial dependencies by ensuring that all non-key attributes are fully dependent on the entire primary key.
- Created separate tables for subjects and teachers, linking them with IDs.
- Reduced redundancy and improved data integrity.
- Made the database easier to maintain and query.

This approach ensures that each piece of information is stored only once, and any updates or changes are made in a single place, which is then reflected throughout the database.




#
#

Sure, let's transform our school database into Third Normal Form (3NF) by eliminating transitive dependencies. We'll start from our 2NF setup and make the necessary changes.

### Initial Setup in 2NF

Our current database setup in 2NF includes the following tables:

#### Students Table:

```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    StudentName VARCHAR(50)
);

INSERT INTO Students (StudentID, StudentName)
VALUES
(1, 'Alice Smith'),
(2, 'Bob Johnson');
```

#### StudentSubjects Table:

```sql
CREATE TABLE StudentSubjects (
    StudentID INT,
    SubjectID INT,
    PRIMARY KEY (StudentID, SubjectID),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
    FOREIGN KEY (SubjectID) REFERENCES Subjects(SubjectID)
);

INSERT INTO StudentSubjects (StudentID, SubjectID)
VALUES
(1, 101),
(1, 102),
(2, 103),
(2, 104),
(2, 105);
```

#### Subjects Table:

```sql
CREATE TABLE Subjects (
    SubjectID INT PRIMARY KEY,
    SubjectName VARCHAR(50),
    Classroom VARCHAR(50)
);

INSERT INTO Subjects (SubjectID, SubjectName, Classroom)
VALUES
(101, 'Math', 'A101'),
(102, 'English', 'B202'),
(103, 'Science', 'C303'),
(104, 'History', 'D404'),
(105, 'Art', 'E505');
```

#### StudentTeachers Table:

```sql
CREATE TABLE StudentTeachers (
    StudentID INT,
    TeacherID INT,
    PRIMARY KEY (StudentID, TeacherID),
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
    FOREIGN KEY (TeacherID) REFERENCES Teachers(TeacherID)
);

INSERT INTO StudentTeachers (StudentID, TeacherID)
VALUES
(1, 201),
(1, 202),
(2, 203),
(2, 204),
(2, 205);
```

#### Teachers Table:

```sql
CREATE TABLE Teachers (
    TeacherID INT PRIMARY KEY,
    TeacherName VARCHAR(50)
);

INSERT INTO Teachers (TeacherID, TeacherName)
VALUES
(201, 'Mr. Brown'),
(202, 'Ms. Green'),
(203, 'Mr. White'),
(204, 'Mr. Black'),
(205, 'Ms. Blue');
```

### Converting to 3NF

To ensure our database is in 3NF, we need to remove any transitive dependencies. Specifically, we need to ensure that non-key attributes are not dependent on other non-key attributes.

#### Creating Normalized Tables in 3NF

1. **Ensure `Subjects` table does not include `TeacherName` directly. Instead, we link subjects to teachers through a junction table.**

**Subjects Table:**

```sql
CREATE TABLE Subjects (
    SubjectID INT PRIMARY KEY,
    SubjectName VARCHAR(50),
    Classroom VARCHAR(50)
);

INSERT INTO Subjects (SubjectID, SubjectName, Classroom)
VALUES
(101, 'Math', 'A101'),
(102, 'English', 'B202'),
(103, 'Science', 'C303'),
(104, 'History', 'D404'),
(105, 'Art', 'E505');
```

2. **Create `SubjectTeachers` table to link subjects with teachers:**

```sql
CREATE TABLE SubjectTeachers (
    SubjectID INT,
    TeacherID INT,
    PRIMARY KEY (SubjectID, TeacherID),
    FOREIGN KEY (SubjectID) REFERENCES Subjects(SubjectID),
    FOREIGN KEY (TeacherID) REFERENCES Teachers(TeacherID)
);

INSERT INTO SubjectTeachers (SubjectID, TeacherID)
VALUES
(101, 201),
(102, 202),
(103, 203),
(104, 204),
(105, 205);
```

3. **Create the `Teachers` table:**

```sql
CREATE TABLE Teachers (
    TeacherID INT PRIMARY KEY,
    TeacherName VARCHAR(50)
);

INSERT INTO Teachers (TeacherID, TeacherName)
VALUES
(201, 'Mr. Brown'),
(202, 'Ms. Green'),
(203, 'Mr. White'),
(204, 'Mr. Black'),
(205, 'Ms. Blue');
```

### Final Database Structure in 3NF

- **Students Table**: Stores unique student information.
- **Subjects Table**: Stores subject and classroom information.
- **StudentSubjects Table**: Links students to their subjects.
- **StudentTeachers Table**: Links students to their teachers.
- **Teachers Table**: Stores teacher information.
- **SubjectTeachers Table**: Links subjects to their teachers.

### Summary

By converting our database to 3NF, we have eliminated transitive dependencies and ensured that all non-key attributes are directly dependent on the primary key. This results in a more organized, efficient, and easy-to-maintain database structure.