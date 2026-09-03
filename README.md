# EX-03 - Student Entity and CRUD (Spring Boot + Hibernate)

## AIM

Develop a Spring Boot application that performs CRUD (Create, Read, Update, Delete) operations on a `Student` entity using Spring Data JPA (Hibernate).

## Requirements

- Java 17 or newer
- Maven
- Spring Boot (starter projects)
- H2 (in-memory) or MySQL for persistence

## Algorithm

1. Create a Spring Boot project (Maven).
2. Add dependencies: Spring Web, Spring Data JPA, H2 or MySQL.
3. Configure `application.properties` for the datasource and Hibernate DDL.
4. Create the `Student` entity and annotate with `@Entity`.
5. Create `StudentRepository` by extending `JpaRepository`.
6. Implement `StudentController` to expose REST endpoints.
7. Run and test the endpoints (Postman / curl / browser).

## Project Structure

- `mvnw`, `mvnw.cmd` — Maven wrapper
- `pom.xml` — module pom with dependencies and plugins
- `.mvn/wrapper/maven-wrapper.properties`
- `src/main/java/com/example/orm/`
	- `OrmApplication.java` — Spring Boot entry point
	- `Student.java` — JPA entity
	- `StudentRepository.java` — `JpaRepository` interface
	- `StudentService.java` — service layer
	- `StudentController.java` — REST controller
- `src/main/resources/application.properties` — datasource and JPA settings for the `orm` module
- `src/test/java/com/example/orm/OrmApplicationTests.java` — basic module tests

## application.properties

```properties
spring.application.name=orm

spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>4.0.6</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>orm</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>orm</name>
	<description/>
	<url/>
	<licenses>
		<license/>
	</licenses>
	<developers>
		<developer/>
	</developers>
	<scm>
		<connection/>
		<developerConnection/>
		<tag/>
		<url/>
	</scm>
	<properties>
		<java.version>17</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-h2console</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webmvc</artifactId>
		</dependency>

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webmvc-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<excludes>
						<exclude>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
						</exclude>
					</excludes>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<executions>
					<execution>
						<id>default-compile</id>
						<phase>compile</phase>
						<goals>
							<goal>compile</goal>
						</goals>
						<configuration>
							<annotationProcessorPaths>
								<path>
									<groupId>org.projectlombok</groupId>
									<artifactId>lombok</artifactId>
								</path>
							</annotationProcessorPaths>
						</configuration>
					</execution>
					<execution>
						<id>default-testCompile</id>
						<phase>test-compile</phase>
						<goals>
							<goal>testCompile</goal>
						</goals>
						<configuration>
							<annotationProcessorPaths>
								<path>
									<groupId>org.projectlombok</groupId>
									<artifactId>lombok</artifactId>
								</path>
							</annotationProcessorPaths>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

</project>

```

### Student.java

```java
package com.example.orm;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Student {
    @Id
    private int id;
    private String name;
    private String department;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }
}
```

### StudentRepository.java

```java
package com.example.orm;

import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Integer>{

}
```

### StudentController.java

```java
package com.example.orm;

import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    // Save Data
    @PostMapping("/save")
    public Student saveStudent(@RequestBody Student s) {
        return service.saveStudent(s);
    }

    // Get All Data
    @GetMapping("/all")
    public List<Student> getAllStudents() {
        return service.getAllStudents();
    }

    // Update
    @PutMapping("/{id}")
    public Student updateStudent(@PathVariable int id, @RequestBody Student s) {
        return service.updateStudent(id, s);
    }

    // DELETE - Delete
    @DeleteMapping("/{id}")
    public String deleteStudent(@PathVariable int id) {
        return service.deleteStudent(id);
    }
}
```

### StudentService.java

```java
package com.example.orm;

import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class StudentService {

    private final StudentRepository repo;

    public StudentService(StudentRepository repo) {
        this.repo = repo;
    }

    // POST
    public Student saveStudent(Student s) {
        return repo.save(s);
    }

    // GET ALL
    public List<Student> getAllStudents() {
        return repo.findAll();
    }

    // UPDATE
    public Student updateStudent(int id, Student s) {
        Student data = repo.findById(id).orElse(null);

        if (data != null) {
            data.setName(s.getName());
            data.setId(s.getId());
            data.setDepartment(s.getDepartment());
            return repo.save(data);
        }

        return null;
    }

    // DELETE
    public String deleteStudent(int id) {
        repo.deleteById(id);
        return "Deleted Successfully";
    }
}
```

### OrmApplication.java

```java
package com.example.orm;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class OrmApplication {

	public static void main(String[] args) {
		SpringApplication.run(OrmApplication.class, args);
	}

}
```

## Output

- `POST /students` — create a student

<img width="1335" height="772" alt="image" src="https://github.com/user-attachments/assets/30846851-6596-4c25-b760-a68143693cef" />


- `GET /students` — list all students

<img width="1356" height="456" alt="image" src="https://github.com/user-attachments/assets/80a9302a-0e78-4b31-8f6a-e5b3f6a5cfdf" />


- `PUT /students/{id}` — update student

<img width="1203" height="385" alt="image" src="https://github.com/user-attachments/assets/e42ebb71-4109-4428-814f-c085a9971f08" />


- `DELETE /students/{id}` — delete student

<img width="1206" height="416" alt="image" src="https://github.com/user-attachments/assets/e4b3742b-fd4b-422f-9e65-4d91a4012d88" />


## Result

Thus the development of a Spring Boot application that performs CRUD operations is completed successfully
