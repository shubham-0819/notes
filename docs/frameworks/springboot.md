# Spring Boot

> **TODO:** This document is a stub. Expand with security, data access, testing, microservices, and production deployment notes.

## What is Spring Boot?

Spring Boot is an opinionated framework built on top of Spring that simplifies the setup and development of production-ready Java applications.

## Key Features

- Auto-configuration — no XML boilerplate
- Embedded server (Tomcat/Jetty) — run as a plain JAR
- Spring Initializr for quick project scaffolding
- Actuator for production monitoring

## Project Structure

```
src/
├── main/
│   ├── java/com/example/demo/
│   │   ├── DemoApplication.java      # Entry point
│   │   ├── controller/               # REST controllers
│   │   ├── service/                  # Business logic
│   │   ├── repository/               # Data access
│   │   └── model/                    # Entities / DTOs
│   └── resources/
│       └── application.properties    # Configuration
└── test/
```

## Basic REST Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAll() {
        return userService.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<User> getById(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public User create(@RequestBody User user) {
        return userService.save(user);
    }
}
```

## application.properties (Common Settings)

```properties
server.port=8080
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=user
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

## Common Annotations

| Annotation | Purpose |
|------------|---------|
| `@SpringBootApplication` | Entry point, enables auto-config |
| `@RestController` | Marks class as REST controller |
| `@GetMapping`, `@PostMapping` etc. | HTTP method mappings |
| `@Service` | Business logic layer |
| `@Repository` | Data access layer |
| `@Autowired` | Dependency injection |
| `@Entity` | JPA entity |
| `@Value` | Inject config values |

## References

- [Spring Boot Official Docs](https://spring.io/projects/spring-boot)
- [Spring Initializr](https://start.spring.io/)
