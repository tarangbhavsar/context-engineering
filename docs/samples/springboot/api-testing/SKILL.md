---
name: spring-api-testing
description: Guide for testing Spring Boot REST APIs using MockMvc and Testcontainers. Use this when creating or debugging API tests.
---

# Spring Boot API Testing Skill

This skill helps you create comprehensive tests for Spring Boot REST APIs.

## When to Use This Skill

- Creating new controller tests
- Adding integration tests with real database
- Debugging failing API tests
- Setting up test infrastructure for new endpoints

## Test Categories

### 1. Controller Unit Tests (@WebMvcTest)

Use for testing controller logic in isolation.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void findById_WhenUserExists_ReturnsUser() throws Exception {
        // Given
        var user = new UserDTO(1L, "John", "john@example.com");
        when(userService.findById(1L)).thenReturn(user);

        // When & Then
        mockMvc.perform(get("/api/users/1")
                .contentType(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(1))
            .andExpect(jsonPath("$.name").value("John"));
    }

    @Test
    void findById_WhenUserNotFound_Returns404() throws Exception {
        // Given
        when(userService.findById(1L))
            .thenThrow(new UserNotFoundException(1L));

        // When & Then
        mockMvc.perform(get("/api/users/1"))
            .andExpect(status().isNotFound())
            .andExpect(jsonPath("$.error").value("User not found"));
    }

    @Test
    void create_WithValidRequest_Returns201() throws Exception {
        // Given
        var request = """
            {
                "name": "John",
                "email": "john@example.com"
            }
            """;
        var created = new UserDTO(1L, "John", "john@example.com");
        when(userService.create(any())).thenReturn(created);

        // When & Then
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(request))
            .andExpect(status().isCreated())
            .andExpect(header().exists("Location"))
            .andExpect(jsonPath("$.id").value(1));
    }

    @Test
    void create_WithInvalidRequest_Returns400() throws Exception {
        // Given - missing required field
        var request = """
            {
                "name": ""
            }
            """;

        // When & Then
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(request))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.errors").isArray());
    }
}
```

### 2. Integration Tests with Testcontainers

Use for testing the full stack with real dependencies.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class UserIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private UserRepository userRepository;

    @BeforeEach
    void setUp() {
        userRepository.deleteAll();
    }

    @Test
    void fullCrudWorkflow() {
        // Create
        var createRequest = new CreateUserRequest("John", "john@example.com");
        var createResponse = restTemplate.postForEntity(
            "/api/users", createRequest, UserDTO.class);
        
        assertThat(createResponse.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        var userId = createResponse.getBody().id();

        // Read
        var getResponse = restTemplate.getForEntity(
            "/api/users/" + userId, UserDTO.class);
        
        assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(getResponse.getBody().name()).isEqualTo("John");

        // Update
        var updateRequest = new UpdateUserRequest("Jane", "jane@example.com");
        restTemplate.put("/api/users/" + userId, updateRequest);

        var updatedResponse = restTemplate.getForEntity(
            "/api/users/" + userId, UserDTO.class);
        assertThat(updatedResponse.getBody().name()).isEqualTo("Jane");

        // Delete
        restTemplate.delete("/api/users/" + userId);

        var deletedResponse = restTemplate.getForEntity(
            "/api/users/" + userId, UserDTO.class);
        assertThat(deletedResponse.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
    }
}
```

### 3. Repository Tests (@DataJpaTest)

Use for testing data access layer.

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class UserRepositoryTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private UserRepository userRepository;

    @Test
    void findByEmail_WhenExists_ReturnsUser() {
        // Given
        var user = User.builder()
            .name("John")
            .email("john@example.com")
            .build();
        userRepository.save(user);

        // When
        var result = userRepository.findByEmail("john@example.com");

        // Then
        assertThat(result).isPresent();
        assertThat(result.get().getName()).isEqualTo("John");
    }
}
```

## Running Tests

```bash
# Run all tests
./mvnw test

# Run specific test class
./mvnw test -Dtest=UserControllerTest

# Run with coverage report
./mvnw test jacoco:report

# Run integration tests only
./mvnw test -Dgroups=integration
```

## Best Practices

1. **Use descriptive test names**: `methodName_givenCondition_expectedBehavior`
2. **Follow AAA pattern**: Arrange, Act, Assert
3. **One assertion concept per test**
4. **Use test data builders** for complex objects
5. **Clean up test data** between tests
6. **Mock external dependencies** in unit tests
7. **Use real dependencies** in integration tests

## Common Issues

| Issue | Solution |
|-------|----------|
| Testcontainers slow | Use singleton containers |
| MockMvc returns 403 | Add `@WithMockUser` or disable security |
| JSON parsing fails | Check DTO serialization annotations |
| Transaction rollback | Use `@Transactional` on test class |
