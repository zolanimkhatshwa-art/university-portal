# University Course Management Portal

> A full-stack Java EE web application implementing the MVC pattern with Java Servlets, JSP views, JDBC database access, and a MySQL relational backend.

![Java](https://img.shields.io/badge/Java-EE-orange?style=flat-square&logo=java)
![MySQL](https://img.shields.io/badge/MySQL-Database-blue?style=flat-square&logo=mysql)
![Pattern](https://img.shields.io/badge/Pattern-MVC_+_DAO-purple?style=flat-square)
![Security](https://img.shields.io/badge/Security-PreparedStatement-green?style=flat-square)

---

## 📋 Overview

A multi-layer enterprise web application for managing university courses and student enrolments. Built to demonstrate core Java EE engineering principles: clean MVC separation, testable DAO data access, parameterised SQL, role-based views, and standard servlet lifecycle management.

---

## 🏗️ Architecture

```
Browser (HTTP Request)
        │
        ▼
┌───────────────────┐
│  CourseServlet    │  ← Controller (Java Servlet)
│  /courses         │    Handles GET, delegates to DAO, forwards to JSP
└────────┬──────────┘
         │
    ┌────▼────┐
    │   DAO   │  ← Data Access Layer
    │ Layer   │    CourseDAO · StudentDAO
    └────┬────┘
         │  JDBC + PreparedStatement
    ┌────▼────┐
    │  MySQL  │  ← Relational Database
    │   DB    │    Courses · Students · Enrollments
    └─────────┘
         │
    (ResultSet mapped to Model)
         │
    ┌────▼────────┐
    │ Course.java │  ← Model (POJO)
    └─────────────┘
         │
    (Set as request attribute, forwarded)
         │
    ┌────▼──────────────────────────┐
    │         JSP Views             │  ← View Layer
    │  courses.jsp                  │
    │  studentSchedule.jsp          │
    │  facultyForm.jsp              │
    └───────────────────────────────┘
```

---

## 📁 Project Structure

```
UniversityPortal/
│
├── src/
│   ├── servlet/
│   │   └── CourseServlet.java      # Controller — handles HTTP, delegates to DAO
│   ├── dao/
│   │   ├── CourseDAO.java          # Data access — getAllCourses(), addCourse()
│   │   └── StudentDAO.java         # Data access — getCoursesByStudent()
│   ├── model/
│   │   └── Course.java             # POJO — course_id, code, name, instructor, schedule
│   └── util/
│       └── DBUtil.java             # JDBC connection factory — DriverManager singleton
│
└── WebContent/
    ├── courses.jsp                  # Admin/all-courses view
    ├── studentSchedule.jsp          # Student role view — enrolled courses only
    ├── facultyForm.jsp              # Faculty role view — update course details
    └── WEB-INF/
        └── web.xml                  # Servlet URL mapping: /courses → CourseServlet
```

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Language | Java (JDK 8+) | Core application logic |
| Web Layer | Java EE Servlets | HTTP request handling, routing |
| View Layer | JSP (JavaServer Pages) | Dynamic HTML rendering |
| Data Access | JDBC | Database communication |
| Database | MySQL | Relational data storage |
| Config | web.xml | Servlet URL mapping |
| Pattern | MVC + DAO | Separation of concerns |

---

## 🔑 Key Engineering Concepts

### MVC Separation
Each layer has a single responsibility:
- **Model** (`Course.java`) — plain Java object representing domain data
- **View** (`*.jsp`) — presentation only, no business logic
- **Controller** (`CourseServlet`) — receives request, calls DAO, forwards to view

### DAO Pattern
Database access is fully abstracted behind `CourseDAO` and `StudentDAO`. The servlet never writes SQL directly — it calls methods like `getAllCourses()` and `getCoursesByStudent(studentId)`. This makes each layer independently testable.

### SQL Injection Prevention
All queries use `PreparedStatement` with parameterised inputs:
```java
String sql = "SELECT c.* FROM Courses c JOIN Enrollments e ON c.course_id = e.course_id WHERE e.student_id = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, studentId);
```
No string concatenation. No injection vulnerability.

### Role-Based Views
Three JSP views serve different user roles:
| View | Role | Content |
|---|---|---|
| `courses.jsp` | Admin | All courses in the system |
| `studentSchedule.jsp` | Student | Only enrolled courses (filtered by student ID) |
| `facultyForm.jsp` | Faculty | Form to update course details |

### Servlet Lifecycle & Routing
URL-to-servlet mapping is declared in `web.xml`:
```xml
<servlet-mapping>
  <servlet-name>CourseServlet</servlet-name>
  <url-pattern>/courses</url-pattern>
</servlet-mapping>
```
The servlet uses `RequestDispatcher.forward()` to delegate rendering to the appropriate JSP.

---

## 🗄️ Database Schema

```sql
CREATE TABLE Courses (
  course_id   INT PRIMARY KEY AUTO_INCREMENT,
  course_code VARCHAR(20)  NOT NULL,
  course_name VARCHAR(100) NOT NULL,
  instructor  VARCHAR(100),
  schedule    VARCHAR(100)
);

CREATE TABLE Students (
  student_id   INT PRIMARY KEY AUTO_INCREMENT,
  student_name VARCHAR(100) NOT NULL,
  email        VARCHAR(100)
);

CREATE TABLE Enrollments (
  enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id    INT REFERENCES Students(student_id),
  course_id     INT REFERENCES Courses(course_id)
);
```

---

## ⚙️ Setup & Run

1. Clone the repo
2. Import into Eclipse / IntelliJ as a Dynamic Web Project
3. Configure a MySQL database and run the schema above
4. Update `DBUtil.java` with your DB credentials
5. Deploy to Apache Tomcat (v9+)
6. Navigate to `http://localhost:8080/UniversityPortal/courses`

---

## 👤 Author

**Zolani Harold Mkhatshwa** · Data & Business Analyst  
[Portfolio](https://zolanimkhatshwa-art.github.io) · [LinkedIn](https://www.linkedin.com/in/zolani-harold-mkhatshwa-33766228b)
