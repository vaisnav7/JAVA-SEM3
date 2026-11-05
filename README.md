# KEC Academic Performance Management System

**Course:** 24ITT32 - Programming in Java  
**Student Name:** VAISNAV V M  
**Roll Number:** 25ITL194  
**Department:** Information Technology  
**College:** Kongu Engineering College (Autonomous), Perundurai  

---

## About the Project

This is a console-based Java application built using JDBC and MySQL to manage academic evaluation and student records as per the autonomous regulations of Kongu Engineering College (Regulation 2024).

The system handles student information, course catalogs, enrollments, attendance eligibility, internal assessments (CIA), end semester exam (ESE) marks, grade computation (10-point scale), and final marksheet generation with SGPA.

---

## Features

- **Student Details:** Add new students, view records, search by roll number, update information, and delete records.
- **Course Management:** Add and manage different course categories:
  - `THEORY` (ESE Max: 60, Minimum passing: 27)
  - `PRACTICAL` (ESE Max: 40, Minimum passing: 20)
  - `EMBEDDED` (ESE Max: 50, Minimum passing: 22.5)
- **Enrollment:** Enroll students in courses semester-wise.
- **Attendance & Condonation:**
  - $\ge 75\%$: Eligible for ESE
  - $65\% - 74\%$: Medical condonation required to be eligible
  - $< 65\%$: Debarred with Shortage of Attendance (`SA`)
- **Marks Entry:**
  - Continuous Internal Assessment (CIA 1, CIA 2, CIA 3)
  - Assignments and Quizzes
  - Practical/Lab scores
  - End Semester Examination (ESE) marks with absent tracking
- **Weightage Configuration:** Dynamic weightage setup for different course types summing to 100%.
- **Grade & SGPA Calculation:**
  - Automatic calculation of aggregate score and letter grade (O, A+, A, B+, B, RA, SA, AB).
  - SGPA calculation based on course credits and grade points.
- **Marksheet Generation:** Generates a consolidated semester marksheet with course-wise grades and SGPA.

---

## Grading Scale (KEC Autonomous)

| Marks Range (Aggregate) | Letter Grade | Grade Point | Result Status |
|:---:|:---:|:---:|:---:|
| 91 – 100 | O | 10 | PASS |
| 81 – 90 | A+ | 9 | PASS |
| 71 – 80 | A | 8 | PASS |
| 61 – 70 | B+ | 7 | PASS |
| 50 – 60 | B | 6 | PASS |
| < 50 | RA | 0 | FAIL |
| Attendance < 65% | SA | 0 | DEBARRED |
| ESE Absent | AB | 0 | FAIL |

---

## Database Tables (MySQL)

Database name: `kec_academics`

```sql
CREATE DATABASE IF NOT EXISTS kec_academics;
USE kec_academics;

-- 1. Student Record Table
CREATE TABLE IF NOT EXISTS students_record (
    roll_no VARCHAR(20) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50) NOT NULL,
    current_semester INT NOT NULL CHECK (current_semester BETWEEN 1 AND 8)
);

-- 2. Courses Table
CREATE TABLE IF NOT EXISTS courses (
    course_code VARCHAR(20) PRIMARY KEY,
    course_title VARCHAR(150) NOT NULL,
    credits INT NOT NULL CHECK (credits > 0),
    course_type ENUM('THEORY', 'PRACTICAL', 'EMBEDDED') NOT NULL
);

-- 3. Enrollments Table
CREATE TABLE IF NOT EXISTS enrollments (
    enrollment_id INT AUTO_INCREMENT PRIMARY KEY,
    roll_no VARCHAR(20) NOT NULL,
    course_code VARCHAR(20) NOT NULL,
    semester INT NOT NULL CHECK (semester BETWEEN 1 AND 8),
    total_classes INT DEFAULT 0,
    attended_classes INT DEFAULT 0,
    medical_condonation BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (roll_no) REFERENCES students_record(roll_no) ON DELETE CASCADE,
    FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE,
    UNIQUE KEY uq_student_course (roll_no, course_code, semester)
);

-- 4. Assessment Marks Table
CREATE TABLE IF NOT EXISTS assessment_marks (
    enrollment_id INT PRIMARY KEY,
    cia1 DECIMAL(5, 2),
    cia2 DECIMAL(5, 2),
    cia3 DECIMAL(5, 2),
    assignment_marks DECIMAL(5, 2),
    quiz_marks DECIMAL(5, 2),
    lab_marks DECIMAL(5, 2),
    ese_marks DECIMAL(5, 2),
    ese_absent BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (enrollment_id) REFERENCES enrollments(enrollment_id) ON DELETE CASCADE
);

-- 5. Assessment Weightages Table
CREATE TABLE IF NOT EXISTS assessment_weightages (
    course_type VARCHAR(20) NOT NULL,
    component_name VARCHAR(50) NOT NULL,
    component_weight DECIMAL(5, 2) NOT NULL,
    PRIMARY KEY (course_type, component_name)
);

-- 6. Final Grades Table
CREATE TABLE IF NOT EXISTS final_grades (
    enrollment_id INT PRIMARY KEY,
    aggregate_marks DECIMAL(5, 2) NOT NULL,
    grade VARCHAR(5) NOT NULL,
    grade_point INT NOT NULL,
    result_status VARCHAR(20) NOT NULL,
    FOREIGN KEY (enrollment_id) REFERENCES enrollments(enrollment_id) ON DELETE CASCADE
);
```

---

## Project Structure

```
├── academic/                                 # Java source code
│   ├── MainApp.java                          # Main CLI application
│   ├── DBConnection.java                     # Database connection
│   ├── StudentDAO.java                       # Student CRUD operations
│   ├── CourseDAO.java                        # Course operations
│   ├── EnrollmentDAO.java                    # Enrollment logic
│   ├── AttendanceDAO.java                    # Attendance tracking
│   ├── AssessmentDAO.java                    # Marks entry
│   ├── AssessmentWeightageDAO.java           # Weightage DAO
│   ├── AssessmentWeightageMenu.java          # Weightage menu
│   ├── FinalGradeService.java                # Grade computation
│   ├── GradeCalculator.java                  # Grading logic
│   ├── GradeDAO.java                         # Grade report queries
│   ├── GradeResultDAO.java                   # Grade storage
│   ├── SGPACalculator.java                   # SGPA calculation
│   └── App.java                              # Application starter
│
├── DOCUMENTS/                                # Project documentation
│   ├── JAVA ASSIGNMENT- VAISNAV V M 25ITL194.pdf
│   └── MARKSHEET OF STUDENT.png
│
├── OUTPUTS/                                  # Output screenshots
│   ├── MAIN MENU.png
│   ├── ADD STUDENT.png
│   ├── STUDENT DATABASE.png
│   ├── STUDENT STRUCTURE.png
│   ├── COURSE MANAGEMENT.png
│   ├── COURSE DATABASE.png
│   ├── COURSE STRUCTURE.png
│   ├── ENROLLMENT MANAGEMENT.png
│   ├── ENROLLEMNT DATABASE.png
│   ├── ENROLLMENT STRUCTURE.png
│   ├── ATTANDENCE MANAGEMENT.png
│   ├── ASSESSMENT MANAGEMENT.png
│   ├── ASSESSMENT MARKS STRUCTURE.png
│   ├── SUBJECT REPORT.png
│   └── MARKSHEET OF STUDENT.png
│
├── JAVA ASSIGNMENT- VAISNAV V M 25ITL194.docx # Project Report (Word)
├── JAVA ASSIGNMENT- VAISNAV V M 25ITL194.pdf  # Project Report (PDF)
└── README.md
```

---

## Screenshots

### Main Menu
![Main Menu](OUTPUTS/MAIN%20MENU.png)

### Student Management
![Add Student](OUTPUTS/ADD%20STUDENT.png)
![Student Database](OUTPUTS/STUDENT%20DATABASE.png)

### Course Management
![Course Management](OUTPUTS/COURSE%20MANAGEMENT.png)
![Course Database](OUTPUTS/COURSE%20DATABASE.png)

### Enrollment & Attendance
![Enrollment Management](OUTPUTS/ENROLLMENT%20MANAGEMENT.png)
![Attendance Management](OUTPUTS/ATTANDENCE%20MANAGEMENT.png)

### Marks & Grade Report
![Assessment Management](OUTPUTS/ASSESSMENT%20MANAGEMENT.png)
![Subject Report](OUTPUTS/SUBJECT%20REPORT.png)
![Marksheet](OUTPUTS/MARKSHEET%20OF%20STUDENT.png)

---

## How to Run

1. **Setup MySQL:**
   - Create the database `kec_academics` and run the SQL schema above.
2. **Set Database Password:**
   - In PowerShell:
     ```powershell
     $env:KEC_DB_PASSWORD="your_mysql_password"
     ```
   - In Command Prompt:
     ```cmd
     set KEC_DB_PASSWORD=your_mysql_password
     ```
3. **Compile and Run:**
   - Ensure you have MySQL Connector/J jar in your classpath.
   - Run `academic/MainApp.java` from your IDE (VS Code / Eclipse / IntelliJ) or via command line.
