[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Qr3lBpHw)

# University Management System - Lab 8

This project is a Spring Boot microservice-based University Management System for managing students, courses, and course enrollments.

The system contains two services:

- `student-service`: manages student records
- `course-service`: manages courses, enrollments, prerequisite checks, and course lookup by student name

## Technologies Used

- Java 21
- Spring Boot
- Spring Web
- Spring Data JPA
- Spring Validation
- Spring Cloud OpenFeign
- PostgreSQL
- Docker Compose
- Gradle
- Lombok
- Swagger / OpenAPI

## Project Structure

```text
university-system
├── student-service
├── course-service
├── docker-compose.yml
├── build.gradle
└── settings.gradle
```

## Features

- Student CRUD operations
- Course CRUD operations
- Enroll a student into a course
- Store enrollment date
- Validate prerequisite course before enrollment
- Retrieve students enrolled in a course
- Retrieve courses by student name
- Azerbaijani Swagger/OpenAPI documentation
- Meaningful error responses

## Services and Ports

| Service | Port | Swagger URL |
|---|---:|---|
| Student Service | `9090` | `http://localhost:9090/swagger-ui/index.html` |
| Course Service | `8081` | `http://localhost:8081/swagger-ui/index.html` |

## Database Setup

The project uses PostgreSQL databases through Docker Compose.

| Database | Container Port | Host Port |
|---|---:|---:|
| studentDB | `5432` | `5432` |
| courseDB | `5432` | `5433` |

Docker Compose starts:

- `student-db`
- `course-db`
- `student-service`
- `course-service`

## How To Run

From the `university-system` directory:

```bash
cd wm2_spring_2026/university-system
```

Run tests:

```bash
bash ./gradlew test
```

Start all services:

```bash
docker compose up --build
```

Stop all services:

```bash
docker compose down
```

If Docker volumes need to be removed:

```bash
docker compose down -v
```

## Swagger

After starting the project, open:

```text
http://localhost:9090/swagger-ui/index.html
http://localhost:8081/swagger-ui/index.html
```

Swagger documentation is written in Azerbaijani.

## Example API Requests

### Create Student

```bash
curl -X POST http://localhost:9090/api/v1/students \
  -H "Content-Type: application/json" \
  -d '{"firstName":"Nihad","lastName":"Abdullayev","email":"nihad@example.com","age":20}'
```

### Get All Students

```bash
curl http://localhost:9090/api/v1/students
```

### Search Students By Name

```bash
curl "http://localhost:9090/api/v1/students/search?name=Nihad"
```

### Create Course Without Prerequisite

```bash
curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{"title":"Programming Basics","code":"CS101","credits":3,"prerequisiteCourseId":null}'
```

### Create Course With Prerequisite

This example assumes course `1` already exists.

```bash
curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{"title":"Data Structures","code":"CS201","credits":4,"prerequisiteCourseId":1}'
```

### Get All Courses

```bash
curl http://localhost:8081/api/v1/courses
```

### Enroll Student Into Course

```bash
curl -X POST http://localhost:8081/api/v1/courses/1/students/1
```

The response includes the enrollment date:

```json
{
  "enrollmentId": 1,
  "courseId": 1,
  "studentId": 1,
  "enrollmentDate": "2026-05-20",
  "message": "Student enrolled successfully."
}
```

### Test Prerequisite Validation

If course `2` requires course `1`, this request fails unless student `1` is already enrolled in course `1`.

```bash
curl -X POST http://localhost:8081/api/v1/courses/2/students/1
```

Expected error response:

```json
{
  "status": 400,
  "error": "Bad Request",
  "message": "Student 1 cannot enroll in course 2 because prerequisite course 1 is not completed."
}
```

### Get Students Enrolled In Course

```bash
curl http://localhost:8081/api/v1/courses/2/students
```

### Get Courses By Student Name

```bash
curl "http://localhost:8081/api/v1/courses/by-student-name?name=Nihad"
```

## Full Manual Test Flow

```bash
curl -X POST http://localhost:9090/api/v1/students \
  -H "Content-Type: application/json" \
  -d '{"firstName":"Nihad","lastName":"Abdullayev","email":"nihad@example.com","age":20}'

curl -X POST http://localhost:9090/api/v1/students \
  -H "Content-Type: application/json" \
  -d '{"firstName":"Ali","lastName":"Valiyev","email":"ali@example.com","age":21}'

curl "http://localhost:9090/api/v1/students/search?name=Nihad"

curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{"title":"Programming Basics","code":"CS101","credits":3,"prerequisiteCourseId":null}'

curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{"title":"Data Structures","code":"CS201","credits":4,"prerequisiteCourseId":1}'

curl http://localhost:8081/api/v1/courses

curl -X POST http://localhost:8081/api/v1/courses/2/students/1

curl -X POST http://localhost:8081/api/v1/courses/1/students/1

curl -X POST http://localhost:8081/api/v1/courses/2/students/1

curl http://localhost:8081/api/v1/courses/2/students

curl "http://localhost:8081/api/v1/courses/by-student-name?name=Nihad"
```

## Error Handling

The system returns structured error responses for cases such as:

- student not found
- course not found
- duplicate enrollment
- prerequisite not completed
- validation errors
- student-service communication errors

## Git Commit Structure

The project was implemented with incremental commits:

1. Initial project import commit
2. Enrollment date feature
3. Prerequisite validation feature
4. Course retrieval by student name feature
5. Swagger Azerbaijani documentation feature
6. README documentation

## Notes

- `course-service` communicates with `student-service` using OpenFeign and RestTemplate.
- Enrollment records are stored in the course database.
- The prerequisite course ID is nullable. If a course has no prerequisite, `prerequisiteCourseId` is `null`.
- Before enrollment, the system validates that the student exists and has completed the prerequisite course if one is required.
