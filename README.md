
 ## 🎓 University Examination System Database

This project presents a comprehensive **relational database design** for a university's examination system. It models academic entities such as departments, faculty, students, courses, and exams — along with their relationships — to support essential administrative and academic functionalities.

---

### 📌 Key Features

- Models university structure: departments, courses, faculty, students.
- Tracks who teaches, coordinates, and creates exams for each course.
- Manages student enrollments, attendance, and exam attempts.
- Supports both internal and external exam types.
- Maintains data integrity with foreign keys and constraints.

---

### 🧱 Database Schema Overview

- `Department`: Contains department names and their heads (faculty).
- `FacultyMember`: Stores information about faculty employees.
- `Course`: Contains course details, offered under a department and coordinated by a faculty member.
- `Student`: Stores student information, linked to their department.
- `Exam`: Represents exams created by faculty, linked to courses.
- `FacultyTeachesCourse`: A many-to-many relationship between faculty and courses they teach.
- `Enrollment`: Represents student enrollment and attendance in courses.
- `ExamAttempt`: Tracks multiple attempts of a student in exams with marks and attempt dates.

---

![image](https://github.com/user-attachments/assets/2067e786-8ea6-4d37-9f47-a957b0a13336)
![image](https://github.com/user-attachments/assets/aae8ecb5-5327-4f74-a8ff-150a4dd20981)




### 🧾 SQL Table Definitions

```sql
CREATE TABLE Department (
    department_id INT PRIMARY KEY AUTO_INCREMENT,
    department_name VARCHAR(255) NOT NULL UNIQUE,
    head_faculty_id INT UNIQUE,
    FOREIGN KEY (head_faculty_id) REFERENCES FacultyMember(employee_id)
);

CREATE TABLE FacultyMember (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    designation VARCHAR(255)
);

CREATE TABLE Course (
    course_code VARCHAR(50) PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    department_id INT NOT NULL,
    coordinator_faculty_id INT NOT NULL,
    FOREIGN KEY (department_id) REFERENCES Department(department_id),
    FOREIGN KEY (coordinator_faculty_id) REFERENCES FacultyMember(employee_id)
);

CREATE TABLE Student (
    roll_number VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    department_id INT NOT NULL,
    FOREIGN KEY (department_id) REFERENCES Department(department_id)
);

CREATE TABLE Exam (
    exam_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    subject_name VARCHAR(255),
    duration_minutes INT,
    exam_date DATE NOT NULL,
    exam_type VARCHAR(50),
    course_code VARCHAR(50) NOT NULL,
    creator_faculty_id INT NOT NULL,
    FOREIGN KEY (course_code) REFERENCES Course(course_code),
    FOREIGN KEY (creator_faculty_id) REFERENCES FacultyMember(employee_id),
    CHECK (exam_type IN ('Internal', 'External'))
);

CREATE TABLE FacultyTeachesCourse (
    faculty_id INT,
    course_code VARCHAR(50),
    PRIMARY KEY (faculty_id, course_code),
    FOREIGN KEY (faculty_id) REFERENCES FacultyMember(employee_id),
    FOREIGN KEY (course_code) REFERENCES Course(course_code)
);

CREATE TABLE Enrollment (
    student_id VARCHAR(50),
    course_code VARCHAR(50),
    enrollment_date DATE NOT NULL,
    attendance_percentage DECIMAL(5,2) NOT NULL,
    PRIMARY KEY (student_id, course_code),
    FOREIGN KEY (student_id) REFERENCES Student(roll_number),
    FOREIGN KEY (course_code) REFERENCES Course(course_code),
    CHECK (attendance_percentage BETWEEN 0 AND 100)
);

CREATE TABLE ExamAttempt (
    student_id VARCHAR(50),
    exam_id INT,
    attempt_number INT,
    attempt_date DATE NOT NULL,
    marks DECIMAL(5,2),
    PRIMARY KEY (student_id, exam_id, attempt_number),
    FOREIGN KEY (student_id) REFERENCES Student(roll_number),
    FOREIGN KEY (exam_id) REFERENCES Exam(exam_id),
    CHECK (attempt_number > 0)
);
