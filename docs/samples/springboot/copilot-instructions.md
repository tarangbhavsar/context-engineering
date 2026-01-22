# Spring Boot Project Guidelines

> **Copilot Instructions for Spring Boot Enterprise Applications**

This project follows Spring Boot best practices for building production-ready REST APIs.

## Project Context

Refer to the following documentation for project understanding:
- [Architecture Guide](docs/ARCHITECTURE.md)
- [API Design Standards](docs/API_DESIGN.md)
- [Contributing Guidelines](CONTRIBUTING.md)

---

## Technology Stack

- **Framework**: Spring Boot 3.x with Java 21+
- **Build Tool**: Maven or Gradle
- **Database**: PostgreSQL with Spring Data JPA
- **Security**: Spring Security with JWT
- **Documentation**: OpenAPI/Swagger 3.0
- **Testing**: JUnit 5, Mockito, Testcontainers

---

## Coding Standards

### Naming Conventions

- **Classes**: PascalCase (`UserController`, `OrderService`)
- **Methods**: camelCase (`findUserById`, `createOrder`)
- **Constants**: SCREAMING_SNAKE_CASE (`MAX_RETRY_COUNT`)
- **Packages**: lowercase, dot-separated (`com.example.user.service`)

### Package Structure

Follow the layered architecture pattern:
```
src/main/java/com/example/
├── config/          # Configuration classes
├── controller/      # REST controllers
├── service/         # Business logic
├── repository/      # Data access
├── model/           # Entity classes
│   ├── entity/      # JPA entities
│   ├── dto/         # Data transfer objects
│   └── mapper/      # MapStruct mappers
├── exception/       # Custom exceptions
└── util/            # Utility classes
```

### REST API Design

1. Use proper HTTP methods (GET, POST, PUT, DELETE, PATCH)
2. Return appropriate HTTP status codes
3. Use DTOs for request/response (never expose entities)
4. Include OpenAPI annotations on all endpoints
5. Implement proper pagination for list endpoints

### Error Handling

1. Use `@ControllerAdvice` for global exception handling
2. Return consistent error response structure
3. Include correlation IDs for tracing
4. Log errors with appropriate levels

### Security

1. Validate all input using `@Valid` annotations
2. Implement method-level security with `@PreAuthorize`
3. Use parameterized queries to prevent SQL injection
4. Never log sensitive data (passwords, tokens, PII)

### Testing

1. Write unit tests for all service methods
2. Use `@WebMvcTest` for controller tests
3. Use `@DataJpaTest` for repository tests
4. Use Testcontainers for integration tests
5. Maintain minimum 80% code coverage

---

## Code Generation Guidelines

When generating code:

1. **Always include**:
   - Javadoc for public APIs
   - Logging statements (use SLF4J)
   - Input validation
   - Proper exception handling

2. **Use these patterns**:
   - Constructor injection (not `@Autowired`)
   - `Optional` for nullable returns
   - Stream API for collections
   - Records for immutable data

3. **Avoid**:
   - Field injection
   - Catching generic `Exception`
   - Hardcoded values (use `@Value` or config)
   - Mutable DTOs

---

## Reference Examples

When creating new components, follow the patterns in:
- [ExampleController](src/main/java/com/example/controller/ExampleController.java)
- [ExampleService](src/main/java/com/example/service/ExampleService.java)
- [ExampleRepository](src/main/java/com/example/repository/ExampleRepository.java)
