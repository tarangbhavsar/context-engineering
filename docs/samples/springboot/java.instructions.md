---
applyTo: "**/*.java"
description: "Java coding standards for Spring Boot applications"
name: "Java Standards"
---

# Java Coding Standards

## General Rules

- Use Java 21+ features (records, pattern matching, virtual threads)
- Follow Google Java Style Guide
- Maximum line length: 120 characters
- Use 4-space indentation (no tabs)

## Annotations

Order annotations consistently:
```java
@Entity
@Table(name = "users")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class User { }
```

## Lombok Usage

Preferred annotations:
- `@Data` for DTOs (or `@Value` for immutable)
- `@Builder` for complex object construction
- `@Slf4j` for logging
- `@RequiredArgsConstructor` for constructor injection

Avoid:
- `@Setter` on entities (use builder or explicit setters)
- `@AllArgsConstructor` without `@NoArgsConstructor` on JPA entities

## Null Handling

```java
// ✅ Correct - Use Optional for nullable returns
public Optional<User> findByEmail(String email) {
    return userRepository.findByEmail(email);
}

// ✅ Correct - Use Objects.requireNonNull for parameters
public void processUser(User user) {
    Objects.requireNonNull(user, "User must not be null");
}

// ❌ Avoid - Returning null
public User findByEmail(String email) {
    return userRepository.findByEmail(email).orElse(null);
}
```

## Stream Operations

```java
// ✅ Correct - Use streams for transformations
List<UserDTO> users = userRepository.findAll().stream()
    .filter(User::isActive)
    .map(userMapper::toDto)
    .toList();

// ✅ Correct - Use method references when possible
users.forEach(this::processUser);

// ❌ Avoid - Complex lambda expressions (extract to methods)
users.stream()
    .filter(u -> u.getAge() > 18 && u.isActive() && u.hasPermission("admin"))
    .collect(Collectors.toList());
```

## Exception Handling

```java
// ✅ Correct - Specific exception handling
try {
    userService.updateUser(user);
} catch (UserNotFoundException e) {
    log.warn("User not found: {}", user.getId());
    throw e;
} catch (ValidationException e) {
    log.error("Validation failed: {}", e.getMessage());
    throw new BadRequestException("Invalid user data", e);
}

// ❌ Avoid - Generic exception handling
try {
    userService.updateUser(user);
} catch (Exception e) {
    log.error("Error", e);
}
```

## Resource Management

```java
// ✅ Correct - Use try-with-resources
try (var connection = dataSource.getConnection();
     var statement = connection.prepareStatement(sql)) {
    return statement.executeQuery();
}

// ✅ Correct - Use @Transactional appropriately
@Transactional(readOnly = true)
public List<User> findAllUsers() {
    return userRepository.findAll();
}
```

## Constants and Configuration

```java
// ✅ Correct - Use configuration properties
@Configuration
@ConfigurationProperties(prefix = "app.user")
public record UserProperties(
    int maxRetries,
    Duration timeout,
    String defaultRole
) {}

// ❌ Avoid - Hardcoded values
private static final int MAX_RETRIES = 3; // Use config instead
```

## Documentation

```java
/**
 * Retrieves a user by their unique identifier.
 *
 * @param id the unique user identifier
 * @return the user if found
 * @throws UserNotFoundException if no user exists with the given ID
 */
public User findById(Long id) {
    return userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException(id));
}
```
