## Part 1: Normalization Challenge

### Task 1 – Identify Violations

#### 1. Which columns lead to redundancy?
In the current unnormalized table, the following columns lead to data redundancy:
* **StudentName**: This is repeated every time a student enrolls in a new course.
* **CourseName**: The name of the course is duplicated for every student registered in that course.
* **ProfessorName & ProfessorEmail**: These details are repeated across all courses taught by the same professor and for every student in those courses.

#### 2. Where could update anomalies happen?
Update anomalies could occur in several areas due to the flat structure:
* **Email Change**: If a professor changes their email (e.g., Dr. Le changes from `le@uni.edu` to a new one), we must update multiple rows. Missing even one row leads to inconsistent data.
* **Course Rename**: If "Database Systems" is renamed to "Intro to Databases", we have to update every record associated with that `CourseID`. This is inefficient and prone to error.

#### 3. Is there any transitive dependency?
Yes, there is a clear transitive dependency:
* **Transitive Link**: `ProfessorEmail` depends on `ProfessorName`, and `ProfessorName` depends on the `CourseID`.
* **Violation**: Since `ProfessorEmail` and `ProfessorName` are non-key attributes, having one depend on another violates the **Third Normal Form (3NF)**.

### Task 2 - Decompose to 3NF
| Table Name | Primary Key | Foreign Key | Normal Form | Description |
| :--- | :--- | :--- | :--- | :--- |
| **Students** | `student_id` | None | 3NF | Stores unique student identities and profile information. |
| **Professors** | `professor_id` | None | 3NF | Isolates faculty contact information to prevent update anomalies. |
| **Courses** | `course_id` | `professor_id` | 3NF | Links each course to a specific professor with `RESTRICT` delete constraint. |
| **Enrollments** | `(student_id, course_id)` | `student_id`, `course_id` | 3NF | A junction table storing grades with `CASCADE` delete behavior. |

### Schema Draft
This draft serves as the blueprint for creating the SQL schema.

| Table | Primary Key | Foreign Key(s) | Non-key columns |
| :--- | :--- | :--- | :--- |
| **Students** | `student_id` | None | `student_name` |
| **Courses** | `course_id` | `professor_id` | `course_name` |
| **Professors** | `professor_id` | None | `professor_name`, `professor_email` |
| **Enrollments** | `(student_id, course_id)` | `student_id`, `course_id` | `grade` |


## Part 2: Relationships

**1. AUTHOR - BOOK**
* **Relationship Type:** One-to-Many (1:N)
* **FK Location:** Book table

**2. CITIZEN - PASSPORT**
* **Relationship Type:** One-to-One (1:1)
* **FK Location:** Passport table

**3. CUSTOMER - ORDER**
* **Relationship Type:** One-to-Many (1:N)
* **FK Location:** Order table

**4. STUDENT - CLASS**
* **Relationship Type:** Many-to-Many (N:N)
* **FK Location:** Junction table

**5. TEAM - PLAYER**
* **Relationship Type:** One-to-Many (1:N)
* **FK Location:** Player table