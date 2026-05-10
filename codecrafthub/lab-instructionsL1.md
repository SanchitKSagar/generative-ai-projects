::page{title="Lab [Option C - Java]: CodeCraftHub Learning Management System - Building a Personalized Learning Platform"}

<img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-CC0100EN-SkillsNetwork/images/IDSN-logo.png" width="200/" />  
<br>
<br>

**Estimated time**: 1.5 hours
<br>

Welcome to this final project lab. Your organization is creating CodeCraftHub, a personalized learning platform crafted for developers! Your task is to design the server-side architecture for this learning platform and develop the server-side application using Java and Spring Boot. You will create the server-side components for CodeCraftHub using Generative AI (GenAI).

In this project, you'll leverage the power of GenAI to transform your vision into a reality.

## Learning objectives

After completing this lab, you will be able to perform the following tasks:

- Design and develop software applications using GenAI
- Create documentation for the code with GenAI
- Create test cases with GenAI
- Run and test the application developed with GenAI

## Prerequisites

- Proficiency in Java
- An active GitHub account
- Comfort working with a local IDE
- Working knowledge of curl

## Setting up the AI classroom

As part of this assignment, you will need to set up the GenAI classroom. If you need assistance, please refer to the following instructions: [Get Familiar with GenAI Classroom](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMSkillsNetwork-AI0274EN-SkillsNetwork/labs/Module1/Get_familiar_with_GenAI_Classroom.md.html "<Lab Link>")

::page{title="Gathering requirements for development of the learning platform"}

When using GenAI to gather your requirements for developing the server-side learning platform, keep the following considerations in mind:

- The effectiveness of the responses depends on the prompts provided.
- The prompts provided here are suggestions; use your discretion to adapt them to your needs.
- Apply your subject matter expertise and judgment as a developer.
- You are responsible for validating the correctness of the responses.

>**Note:** In the GenAI interface, select your preferred LLM.

Enter the following prompt to provide the LLM with context and define the objective:

```
I want to create a simple personalized learning platform called CodeCraftHub where developers can track courses they want to learn.

Requirements:
- Use Java with Spring Boot framework
- Store course data in a simple JSON text file (NO database needed)
- No authentication or user management needed
- Focus on learning REST API basics

Each course should track:
- Course name
- Description
- Target completion date
- Current status (Not Started, In Progress, Completed)

Recommend:
1. A simple project structure for beginners
2. The REST API endpoints to be created
3. How to store and retrieve data from a JSON file
```

<details><summary>Click here to view the sample response generated</summary>
<div>
Creating a simple personalized learning platform such as CodeCraftHub is a great beginner project! Here is a straightforward approach:

### 1. Simple Project Structure

```
codecrafthub/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/
│       │       └── codecrafthub/
│       │           ├── CodeCraftHubApplication.java  # Main Spring Boot class
│       │           ├── controller/
│       │           │   └── CourseController.java     # REST endpoints
│       │           ├── model/
│       │           │   └── Course.java               # Course data model
│       │           └── service/
│       │               └── CourseService.java        # Business logic
│       └── resources/
│           └── application.properties                # Configuration
├── courses.json        # JSON file for data storage (auto-created)
├── pom.xml            # Maven dependencies
└── README.md          # Project documentation
```

### 2. REST API Endpoints

You should create these basic endpoints:

**Course Management:**

- `POST /api/courses` - Add a new course
- `GET /api/courses` - Get all courses
- `GET /api/courses/{id}` - Get a specific course
- `PUT /api/courses/{id}` - Update a course
- `DELETE /api/courses/{id}` - Delete a course

**Optional Bonus Endpoints:**

- `GET /api/courses/stats` - Get statistics about courses
- `GET /api/courses/search?q=term` - Search courses

### 3. Data Storage with JSON

**Course Data Structure:**

```json
{
  "id": 1,
  "name": "Python Basics",
  "description": "Learn Python fundamentals",
  "target_date": "2025-12-31",
  "status": "Not Started",
  "created_at": "2025-11-04 10:30:00"
}
```

**How to Store/Retrieve:**

- Use Jackson library to read/write JSON
- Load all courses at the start of each request
- Save back to file after any changes
- The file will be created automatically if it doesn't exist

### 4. Next Steps

Start by creating your model classes and service layer:

- `Course.java` - Define the course data structure
- `CourseService.java` - Implement CRUD operations with JSON file storage
- `CourseController.java` - Create REST API endpoints

Then, implement each API endpoint one at a time, testing as you go!
</div></details>

>Disclaimer: The response generated by your LLM might differ from the sample response shown here.

::page{title="Creating the application structure"}

For the following exercise, a simple Spring Boot application with basic layered architecture is the recommended approach.

In the GenAI classroom, in the **type your prompt field** enter the following prompt  to create the main application file:

```
Now, create the complete Java Spring Boot code with these requirements:

1. Create a Spring Boot REST API with all CRUD operations for courses
2. Store data in a JSON file called "courses.json"
3. Include these endpoints:
   - POST /api/courses - Add a new course
   - GET /api/courses - Get all courses
   - GET /api/courses/{id} - Get a specific course
   - PUT /api/courses/{id} - Update a course
   - DELETE /api/courses/{id} - Delete a course

4. Each course must have these JSON fields:
   - id (auto-generated, starting from 1)
   - name (required)
   - description (required)
   - target_date (required in JSON requests, format YYYY-MM-DD)
   - status (required, must be exactly one of: "Not Started", "In Progress", or "Completed")
   - created_at (auto-generated timestamp in JSON responses)

   In the Java model, you may use camelCase field names such as `targetDate` and `createdAt` with Jackson annotations to map them to the JSON field names.

5. Include proper error handling for:
   - Missing required fields
   - Course not found
   - Invalid status values
   - File read/write errors

6. Add helpful comments throughout the code for beginners
7. Make sure the app creates courses.json automatically if it doesn't exist
8. Use Jackson for JSON processing

Please provide:
- Course.java (model class)
- CourseService.java (service layer with file operations)
- CourseController.java (REST controller)
- CodeCraftHubApplication.java (main class)
- pom.xml (Maven dependencies)
```

The response will include the complete Spring Boot application code.

>**Note:** Use snake_case for JSON request and response fields, such as `target_date` and `created_at`. In the Java model class, camelCase field names such as `targetDate` and `createdAt` are acceptable when they are mapped with Jackson annotations. Use the status values exactly as shown: `Not Started`, `In Progress`, and `Completed`.

<details><summary>Click here to view the sample response generated</summary>
<div>

Here's the complete Spring Boot application code with all the requirements:

**1. Course.java (Model)**

```java
package com.codecrafthub.model;

import com.fasterxml.jackson.annotation.JsonProperty;

public class Course {
    private Integer id;
    private String name;
    private String description;
    
    @JsonProperty("target_date")
    private String targetDate;
    
    private String status;
    
    @JsonProperty("created_at")
    private String createdAt;
    
    // Constructors
    public Course() {}
    
    public Course(Integer id, String name, String description, String targetDate, String status, String createdAt) {
        this.id = id;
        this.name = name;
        this.description = description;
        this.targetDate = targetDate;
        this.status = status;
        this.createdAt = createdAt;
    }
    
    // Getters and Setters
    public Integer getId() { return id; }
    public void setId(Integer id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }
    
    public String getTargetDate() { return targetDate; }
    public void setTargetDate(String targetDate) { this.targetDate = targetDate; }
    
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
    
    public String getCreatedAt() { return createdAt; }
    public void setCreatedAt(String createdAt) { this.createdAt = createdAt; }
}
```

**2. CourseService.java (Service Layer)**

```java
package com.codecrafthub.service;

import com.codecrafthub.model.Course;
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.stereotype.Service;

import java.io.File;
import java.io.IOException;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;

@Service
public class CourseService {
    private static final String DATA_FILE = "courses.json";
    private final ObjectMapper objectMapper = new ObjectMapper();
    
    // Load courses from JSON file
    public List<Course> loadCourses() throws IOException {
        File file = new File(DATA_FILE);
        if (!file.exists()) {
            saveCourses(new ArrayList<>());
            return new ArrayList<>();
        }
        
        try {
            return objectMapper.readValue(file, new TypeReference<List<Course>>() {});
        } catch (IOException e) {
            return new ArrayList<>();
        }
    }
    
    // Save courses to JSON file
    public void saveCourses(List<Course> courses) throws IOException {
        objectMapper.writerWithDefaultPrettyPrinter().writeValue(new File(DATA_FILE), courses);
    }
    
    // Generate next ID
    public Integer getNextId(List<Course> courses) {
        return courses.stream()
                .mapToInt(Course::getId)
                .max()
                .orElse(0) + 1;
    }
}
```

**3. CourseController.java (REST Controller)**

```java
package com.codecrafthub.controller;

import com.codecrafthub.model.Course;
import com.codecrafthub.service.CourseService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.*;

@RestController
@RequestMapping("/api")
public class CourseController {
    
    @Autowired
    private CourseService courseService;
    
    private static final List<String> VALID_STATUSES = Arrays.asList("Not Started", "In Progress", "Completed");
    
    // GET all courses
    @GetMapping("/courses")
    public ResponseEntity<Map<String, Object>> getAllCourses() {
        try {
            List<Course> courses = courseService.loadCourses();
            Map<String, Object> response = new HashMap<>();
            response.put("success", true);
            response.put("count", courses.size());
            response.put("courses", courses);
            return ResponseEntity.ok(response);
        } catch (Exception e) {
            return createErrorResponse("Failed to retrieve courses: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    
    // GET specific course
    @GetMapping("/courses/{id}")
    public ResponseEntity<Map<String, Object>> getCourse(@PathVariable Integer id) {
        try {
            List<Course> courses = courseService.loadCourses();
            Optional<Course> course = courses.stream()
                    .filter(c -> c.getId().equals(id))
                    .findFirst();
            
            if (course.isPresent()) {
                Map<String, Object> response = new HashMap<>();
                response.put("success", true);
                response.put("course", course.get());
                return ResponseEntity.ok(response);
            } else {
                return createErrorResponse("Course with ID " + id + " not found", HttpStatus.NOT_FOUND);
            }
        } catch (Exception e) {
            return createErrorResponse("Failed to retrieve course: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    
    // POST new course
    @PostMapping("/courses")
    public ResponseEntity<Map<String, Object>> addCourse(@RequestBody Course course) {
        try {
            // Validate required fields
            String validationError = validateCourse(course, true);
            if (validationError != null) {
                return createErrorResponse(validationError, HttpStatus.BAD_REQUEST);
            }
            
            List<Course> courses = courseService.loadCourses();
            
            // Set auto-generated fields
            course.setId(courseService.getNextId(courses));
            course.setCreatedAt(LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            
            courses.add(course);
            courseService.saveCourses(courses);
            
            Map<String, Object> response = new HashMap<>();
            response.put("success", true);
            response.put("message", "Course added successfully");
            response.put("course", course);
            return ResponseEntity.status(HttpStatus.CREATED).body(response);
        } catch (Exception e) {
            return createErrorResponse("Failed to add course: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    
    // PUT update course
    @PutMapping("/courses/{id}")
    public ResponseEntity<Map<String, Object>> updateCourse(@PathVariable Integer id, @RequestBody Course updatedCourse) {
        try {
            List<Course> courses = courseService.loadCourses();
            Optional<Course> existingCourse = courses.stream()
                    .filter(c -> c.getId().equals(id))
                    .findFirst();
            
            if (!existingCourse.isPresent()) {
                return createErrorResponse("Course with ID " + id + " not found", HttpStatus.NOT_FOUND);
            }
            
            // Validate status if provided
            if (updatedCourse.getStatus() != null && !VALID_STATUSES.contains(updatedCourse.getStatus())) {
                return createErrorResponse("Status must be one of: " + String.join(", ", VALID_STATUSES), HttpStatus.BAD_REQUEST);
            }
            
            Course course = existingCourse.get();
            
            // Update fields if provided
            if (updatedCourse.getName() != null) course.setName(updatedCourse.getName());
            if (updatedCourse.getDescription() != null) course.setDescription(updatedCourse.getDescription());
            if (updatedCourse.getTargetDate() != null) course.setTargetDate(updatedCourse.getTargetDate());
            if (updatedCourse.getStatus() != null) course.setStatus(updatedCourse.getStatus());
            
            courseService.saveCourses(courses);
            
            Map<String, Object> response = new HashMap<>();
            response.put("success", true);
            response.put("message", "Course updated successfully");
            response.put("course", course);
            return ResponseEntity.ok(response);
        } catch (Exception e) {
            return createErrorResponse("Failed to update course: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    
    // DELETE course
    @DeleteMapping("/courses/{id}")
    public ResponseEntity<Map<String, Object>> deleteCourse(@PathVariable Integer id) {
        try {
            List<Course> courses = courseService.loadCourses();
            Optional<Course> courseToDelete = courses.stream()
                    .filter(c -> c.getId().equals(id))
                    .findFirst();
            
            if (!courseToDelete.isPresent()) {
                return createErrorResponse("Course with ID " + id + " not found", HttpStatus.NOT_FOUND);
            }
            
            courses.remove(courseToDelete.get());
            courseService.saveCourses(courses);
            
            Map<String, Object> response = new HashMap<>();
            response.put("success", true);
            response.put("message", "Course deleted successfully");
            response.put("deleted_course", courseToDelete.get());
            return ResponseEntity.ok(response);
        } catch (Exception e) {
            return createErrorResponse("Failed to delete course: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
    
    // Helper method to validate course
    private String validateCourse(Course course, boolean checkRequired) {
        if (checkRequired) {
            if (course.getName() == null || course.getName().trim().isEmpty()) {
                return "Missing required field: name";
            }
            if (course.getDescription() == null || course.getDescription().trim().isEmpty()) {
                return "Missing required field: description";
            }
            if (course.getTargetDate() == null || course.getTargetDate().trim().isEmpty()) {
                return "Missing required field: target_date";
            }
            if (course.getStatus() == null || course.getStatus().trim().isEmpty()) {
                return "Missing required field: status";
            }
        }
        
        if (course.getStatus() != null && !VALID_STATUSES.contains(course.getStatus())) {
            return "Status must be one of: " + String.join(", ", VALID_STATUSES);
        }
        
        return null;
    }
    
    // Helper method to create error response
    private ResponseEntity<Map<String, Object>> createErrorResponse(String message, HttpStatus status) {
        Map<String, Object> response = new HashMap<>();
        response.put("success", false);
        response.put("error", message);
        return ResponseEntity.status(status).body(response);
    }
}
```

**4. CodeCraftHubApplication.java (Main Class)**

```java
package com.codecrafthub;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CodeCraftHubApplication {
    public static void main(String[] args) {
        System.out.println("============================================================");
        System.out.println("CodeCraftHub API is starting...");
        System.out.println("============================================================");
        System.out.println("Data will be stored in: courses.json");
        System.out.println("API will be available at: http://localhost:8080");
        System.out.println("============================================================");
        System.out.println("\nPress CTRL+C to stop the server\n");
        
        SpringApplication.run(CodeCraftHubApplication.class, args);
    }
}
```

**5. pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>
    
    <groupId>com.codecrafthub</groupId>
    <artifactId>codecrafthub</artifactId>
    <version>1.0.0</version>
    <name>CodeCraftHub</name>
    <description>Learning Management System API</description>
    
    <properties>
        <java.version>17</java.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

</div></details>

>Disclaimer: The response generated by your LLM might differ from the sample response shown here.

## Generate README documentation

After you generate your application code, use GenAI to create project documentation.

Enter the following prompt to create a `README.md` file for the CodeCraftHub project:

```
Create a README.md file for the CodeCraftHub project that includes:
1. Project overview and features
2. Installation instructions
3. How to run the application
4. API endpoint documentation with examples
5. Troubleshooting guide
```

<details><summary>Click here to view the sample response generated</summary>
<div>

# CodeCraftHub Learning Management System

A simple REST API for tracking your learning goals and courses built with Java and Spring Boot.

## Features

- Complete CRUD operations for course management
- JSON file-based storage (no database needed)
- RESTful API design
- Proper error handling

## Installation

1. Clone or download the project
2. Install Java JDK 17 or higher
3. Install Maven (if not already installed)

## Running the Application

Navigate to the project directory and run:

```bash
mvn spring-boot:run
```

Or build and run the JAR:

```bash
mvn clean package
java -jar target/codecrafthub-1.0.0.jar
```

The API will be available at `http://localhost:8080`

## API Endpoints

### 1. Add a Course

**POST** `/api/courses`

Request body:

```json
{
  "name": "Python Basics",
  "description": "Learn Python fundamentals",
  "target_date": "2025-12-31",
  "status": "Not Started"
}
```

### 2. Get All Courses

**GET** `/api/courses`

### 3. Get Specific Course

**GET** `/api/courses/{id}`

### 4. Update Course

**PUT** `/api/courses/{id}`

Request body (all fields optional):

```json
{
  "status": "In Progress"
}
```

### 5. Delete Course

**DELETE** `/api/courses/{id}`

## Testing

Use the provided curl commands or import the Postman collection to test all endpoints.

## Troubleshooting

**Problem:** \"JAVA_HOME not set\"  
**Solution:** Set JAVA_HOME environment variable to your JDK installation path

**Problem:** \"Port already in use\"  
**Solution:** Stop other applications using port 8080 or change the port in application.properties

## Project Structure

```
codecrafthub/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/codecrafthub/
│       │       ├── CodeCraftHubApplication.java
│       │       ├── controller/
│       │       ├── model/
│       │       └── service/
│       └── resources/
├── courses.json     # Data storage (auto-created)
└── pom.xml         # Dependencies
```

</div></details>

>Disclaimer: The response generated by your LLM might differ from the sample response shown here.

::page{title="Running and testing your application on your local machine"}

Complete the following steps to run and test your application on your local machine.

## Step 1: Set up your development environment

### Prerequisites

Before you run the Maven commands, make sure the following software is installed on your machine:

#### Required Software

**Java Development Kit (JDK)**:
Minimum version: JDK 17 (for most modern Spring Boot 3.x applications)
Recommended versions: JDK 17 or JDK 21 (LTS versions)

**Apache Maven**:
Minimum version: Maven 3.6.3 or higher
Recommended: Maven 3.8+

1. Create a new project directory:

```bash
mkdir codecrafthub
cd codecrafthub
```

1. Create the project directory structure:

```bash
mkdir -p src/main/java/com/codecrafthub/controller
mkdir -p src/main/java/com/codecrafthub/model
mkdir -p src/main/java/com/codecrafthub/service
mkdir -p src/main/resources
```

1. Create your Java files and `pom.xml` by using the code generated by GenAI.

## Step 2: Build the project

```bash
mvn clean install
```

## Step 3: Run the application

```bash
mvn spring-boot:run
```

You should see something similar:

```
- CodeCraftHub API is starting...
- Data will be stored in: courses.json
- API will be available at: http://localhost:8080
```

## Step 4: Test the API endpoints

Make sure the server is still running, as shown in Step 3.

In the Generative AI classroom, in the **type your prompt** field enter the following prompts one at a time to generate the curl commands you will use for testing. Then, run each generated command in a new terminal window.

The prompts appear in plain code blocks so you can distinguish them from executable terminal commands. Executable commands appear in `bash` code blocks.

### Prompt 1: Generate a POST command

```
Create a curl command for a POST request to http://localhost:8080/api/courses that adds a course with these values:
- name: Python Basics
- description: Learn Python fundamentals
- target_date: 2025-12-31
- status: Not Started

Return only the curl command.
```

Sample command:

Run a command similar to the following in a new terminal window:

```bash
curl -X POST http://localhost:8080/api/courses \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Python Basics",
    "description": "Learn Python fundamentals",
    "target_date": "2025-12-31",
    "status": "Not Started"
}'
```

### Prompt 2: Generate GET commands

```
Create two curl commands for this API:
1. A GET request to http://localhost:8080/api/courses to retrieve all courses
2. A GET request to http://localhost:8080/api/courses/1 to retrieve the course with ID 1

Return only the curl commands.
```

Sample commands:

Run commands similar to the following in a new terminal window:

```bash
curl http://localhost:8080/api/courses
curl http://localhost:8080/api/courses/1
```

### Prompt 3: Generate a PUT command

```
Create a curl command for a PUT request to http://localhost:8080/api/courses/1 that updates the status of the course with ID 1 to "In Progress".

Return only the curl command.
```

Sample command:

Run a command similar to the following in a new terminal window:

```bash
curl -X PUT http://localhost:8080/api/courses/1 \
  -H "Content-Type: application/json" \
  -d '{
    "status": "In Progress"
  }'
```

### Prompt 4: Generate a DELETE command

```
Create a curl command for a DELETE request to http://localhost:8080/api/courses/1 that deletes the course with ID 1.

Return only the curl command.
```

Sample command:

Run a command similar to the following in a new terminal window:

```bash
curl -X DELETE http://localhost:8080/api/courses/1
```

Verify that:

- All endpoints return appropriate responses
- Data persists in `courses.json`
- Error handling works correctly

::page{title="[Optional] Challenge"}

If you complete the basic requirements, consider adding this bonus feature using GenAI:

### Enhancement: Statistics Endpoint

- Show the total number of courses by status

Prompt hint:

```
Add a new endpoint GET /api/courses/stats that returns statistics about courses:
- Total number of courses
- Number of courses by status (Not Started, In Progress, Completed)

Add this to the CourseController.java file.
```

::page{title="Pushing your work to GitHub"}

Remember, any modifications you make in the lab environment won\'t be saved. If you plan to step away, use the following steps to make sure your changes are pushed to GitHub:

Verify that you are currently inside the project directory.

1. Navigate to the project directory by performing `c&#8203;d codecrafthub`.

2. Set up your Git configuration:
   - Run: `g&#8203;it config --global user.email "yourgithub@email.com"`
   - Run: `g&#8203;it config --global user.name "name"`

3. Initialize the repository (if not already done):
   - Run: `g&#8203;it init`

4. Add your changes to the staging area:
   - Run: `g&#8203;it add .`

5. Commit your changes with a descriptive message:
   - Run: `g&#8203;it commit -m "Complete CodeCraftHub learning platform"`

6. The first step is to generate an access token from GitHub.com. Follow the lab named [Generate GitHub personal access token](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-CD0131EN-SkillsNetwork/labs/create-personal-token/instructions.md.html "Generate GitHub personal access token") and copy the access token as a password in the upcoming exercises.

7. Create a new repository on GitHub.com and copy the repository URL.

8. Link your local repository to GitHub:
   - Run: `g&#8203;it remote add origin <your-github-repo-url>`

9. Push your changes to the Git repository:
   - Run: `g&#8203;it push -u origin main`

10. An on-screen message in the terminal window will prompt you to enter your GitHub username and password (your previously created \"Personal Access Token\" in Step 6).

These steps verify that you safely stored your project in GitHub, allowing you to continue.

::page{title="Exemplar code"}

For your reference, select the following link to download a zip file that provides an example code. After downloading the file, unzip the file in your local machine.

[CodeCraftHub code [Java]](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/8IbnJhmljiwHhfS3C7TT7A/codecrafthub.zip "CodeCraftHub code [Java]")

::page{title="Reviewing your checklist"}

At this stage, you have completed the following tasks:

- You have a running application that offers REST API functionality for the CodeCraftHub learning platform.
- You created a simple, file-based storage system using JSON.
- The application includes proper error handling and validation.
- You documented your code and created test cases.
- [Optional] You pushed all the code to your GitHub repository.

## Summary

You've now achieved the following accomplishments:

- Successfully gathered requirements for a simple learning platform using GenAI
- Created a complete Spring Boot REST API with CRUD operations
- Implemented file-based data storage using JSON
- Created comprehensive test cases and documentation
- Validated that your application works correctly

Congratulations! You have successfully leveraged Generative AI to build the CodeCraftHub Learning Management System using Java and Spring Boot.

## Author(s)

[Ramanujam Srinivasan](https://www.linkedin.com/in/ramanujamrs/)

<p align="center"><sub>© IBM Corporation. All rights reserved.</sub></p>

<!--
## Changelog
| Date | Version | Changed by | Change Description |
|------|--------|--------|---------|
| 2025-11-04 | 0.1 | Ramanujam | Initial version created |
| 2025-11-04 | 0.2 | P.Kravitz | ID edits completed. Small edits completed and IBM style edits to lines  707, 709 and 715 |
| 2026-1-15 | 0.3 | Leah Hanson | QA reviewed. Minor format fixes |
| 2026-1-21 | 0.4 | Sharon Felix | ID review; corrected language inconsistencies |
| 2026-1-21 | 0.5| P.Kravitz | ID edit; lab title updated |
| 2026-3-02 | 0.6 | P.Kravitz | ID edit; two spellings corrected |
| 2026-3-02 | 0.7 | P.Kravitz | Removed CSS; Updated the final learning objective; clarified what the learner is creating |
| 2026-3-02 | 0.7 | P.Kravitz | Four curl commands. Updated README, minor formatting edits. |

-->

## Author(s)

[Ramanujam Srinivasan](https://www.linkedin.com/in/ramanujamrs/)

### © IBM Corporation. All rights reserved

&nbsp;
