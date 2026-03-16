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

**Authors to Books:** Many-to-Many (N:N). An author can write multiple books, and a single book can be co-authored by several people. Therefore, a junction table is needed to connect them.

**Citizens to Passports:** One-to-Many (1:N). A citizen may hold multiple passports (e.g., in cases of dual citizenship), but each physical passport is strictly issued to exactly one citizen. The foreign key is placed in the Passports table.

**Customers to Orders:** One-to-Many (1:N). A customer can place numerous orders over time, yet each specific order belongs to only one customer. The foreign key belongs in the Orders table.

**Students to Classes:** Many-to-Many (N:N). A student can enroll in various classes, and a single class accommodates multiple students. This relationship requires a bridge table to store the foreign keys.

**Teams to Players:** One-to-Many (1:N). A team consists of many players, but an individual player typically plays for exactly one team at a given time. The foreign key must be stored in the Players table.