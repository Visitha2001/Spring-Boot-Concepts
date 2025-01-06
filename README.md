### Core Concepts of Spring Boot with Code Examples

Spring Boot is a powerful framework designed to simplify the setup, development, and deployment of Spring-based applications. It comes with pre-configured setups, eliminating boilerplate configuration and allowing you to focus on writing the business logic. Below are the key concepts of Spring Boot, along with code snippets and explanations.

---

### 1. **Spring Boot Starter Project**

To begin with Spring Boot, you need a starter project. You can generate it using Spring Initializr (https://start.spring.io/).

Dependencies often include:
- **Spring Web**: To create web applications (REST APIs).
- **Spring Data JPA**: For database interaction using JPA.
- **H2/MySQL/PostgreSQL**: As the database.
- **Spring Boot DevTools**: To enable hot reloading.

---

### 2. **@SpringBootApplication Annotation**

This is the entry point of the Spring Boot application. The `@SpringBootApplication` annotation encapsulates:
- `@EnableAutoConfiguration`: Automatically configures Spring application based on the dependencies.
- `@ComponentScan`: Scans for Spring components.
- `@Configuration`: Marks this class as a configuration class.

```java
// MainApplication.java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```

---

### 3. **Creating REST Controllers**

Spring Boot allows you to easily create REST APIs using `@RestController` and `@RequestMapping`.

```java
// HelloController.java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, Spring Boot!";
    }
}
```

- `@RestController`: Tells Spring that this class will handle HTTP requests.
- `@GetMapping`: Handles HTTP GET requests to `/hello`.

---

### 4. **Dependency Injection with @Autowired**

Spring Boot uses **Dependency Injection (DI)** to manage objects and their dependencies. The `@Autowired` annotation is used to inject beans automatically.

```java
// Service.java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class HelloService {
    public String greet() {
        return "Hello from Service!";
    }
}

// Controller.java
package com.example.demo.controller;

import com.example.demo.service.HelloService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    private final HelloService helloService;

    @Autowired
    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    @GetMapping("/greet")
    public String greet() {
        return helloService.greet();
    }
}
```

Here, `HelloService` is a Spring-managed bean, and it's injected into the `HelloController` using `@Autowired`.

---

### 5. **Spring Boot with Database - Spring Data JPA**

Spring Boot simplifies database interaction using Spring Data JPA. You define an entity, repository, and Spring automatically creates the data access layer for you.

#### 5.1 Define Entity

```java
// Employee.java
package com.example.demo.entity;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Employee {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String department;

    // Getters and Setters
}
```

#### 5.2 Define Repository

```java
// EmployeeRepository.java
package com.example.demo.repository;

import com.example.demo.entity.Employee;
import org.springframework.data.jpa.repository.JpaRepository;

public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```

Here, `EmployeeRepository` extends `JpaRepository` which provides built-in methods like `findAll()`, `save()`, `deleteById()`, etc.

#### 5.3 Service Layer

```java
// EmployeeService.java
package com.example.demo.service;

import com.example.demo.entity.Employee;
import com.example.demo.repository.EmployeeRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    public List<Employee> getAllEmployees() {
        return employeeRepository.findAll();
    }

    public Employee saveEmployee(Employee employee) {
        return employeeRepository.save(employee);
    }
}
```

#### 5.4 REST Controller for Employee API

```java
// EmployeeController.java
package com.example.demo.controller;

import com.example.demo.entity.Employee;
import com.example.demo.service.EmployeeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

    @GetMapping
    public List<Employee> getAllEmployees() {
        return employeeService.getAllEmployees();
    }

    @PostMapping
    public Employee createEmployee(@RequestBody Employee employee) {
        return employeeService.saveEmployee(employee);
    }
}
```

Here, the `/employees` endpoint returns all employees or creates a new employee.

---

### 6. **Spring Boot Properties Configuration**

Spring Boot uses the `application.properties` or `application.yml` file to configure the application. For example, if you're using H2 in-memory database, you can configure it as follows:

```properties
# application.properties
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```

Now, you can access the H2 console at `http://localhost:8080/h2-console`.

---

### 7. **Exception Handling in Spring Boot**

Spring Boot provides easy ways to handle exceptions using `@ExceptionHandler` and `@ControllerAdvice`.

```java
// GlobalExceptionHandler.java
package com.example.demo.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleRuntimeException(RuntimeException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

---

### 8. **Spring Boot Profiles**

Spring Boot allows you to run your application in different environments (development, production, etc.) using profiles. You can specify profiles in `application.properties` or `application.yml`:

```properties
# application.properties
spring.profiles.active=dev
```

Define separate property files for each profile like `application-dev.properties` and `application-prod.properties`.

---

### 9. **Spring Boot Security (Basic Authentication)**

You can integrate Spring Security with Spring Boot to add authentication and authorization to your APIs.

Add the dependency:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Then, configure security:

```java
// SecurityConfig.java
package com.example.demo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .anyRequest().authenticated()
                .and()
            .httpBasic();
        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();
        return new InMemoryUserDetailsManager(user);
    }
}
```

This configuration secures all API endpoints and provides basic authentication.

---

### 10. **Testing in Spring Boot**

Spring Boot provides testing support with the `@SpringBootTest` annotation for integration testing.

```java
// EmployeeServiceTest.java
package com.example.demo;

import com.example.demo.entity.Employee;
import com.example.demo.repository.EmployeeRepository;
import com.example.demo.service.EmployeeService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class EmployeeServiceTest {

    @Autowired
    private EmployeeService employeeService;

    @Test
    void testCreateEmployee() {
        Employee employee = new Employee();
        employee.setName("John");
        employee.setDepartment("IT");

        Employee savedEmployee = employeeService.saveEmployee(employee);

        assertNotNull(savedEmployee.getId());
        assertEquals("John", savedEmployee.getName());
    }
}
```

---

### Summary

1. **@SpringBootApplication**: Marks the main class.
2. **REST API**: Easily create REST controllers using `@RestController`.
3. **Dependency Injection**: Managed using `@Autowired`.
4. **Spring Data JPA**: Simplifies database interaction.
5. **Exception Handling**: Managed using

 `@ControllerAdvice`.
6. **Security**: Easily integrate security with `@EnableWebSecurity`.
7. **Profiles**: Enable environment-specific configurations.
8. **Testing**: Spring Boot provides easy integration testing support with `@SpringBootTest`.

These are the core concepts of Spring Boot. It helps create full-stack applications with minimal setup and boilerplate configuration.
