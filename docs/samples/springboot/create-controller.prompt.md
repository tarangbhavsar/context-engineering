---
description: "Generate a new Spring Boot REST controller with CRUD endpoints"
name: "Create Controller"
agent: "agent"
model: "Claude Sonnet 4"
tools: ['codebase', 'terminal', 'editFiles']
---

# Generate Spring Boot REST Controller

Create a new Spring Boot REST controller with full CRUD operations.

## Input Parameters

- **Entity Name**: ${input:entityName:Enter entity name (e.g., User, Order)}
- **Base Package**: ${input:package:com.example}
- **Base Path**: ${input:basePath:Enter API base path (e.g., /api/users)}

## Requirements

### Controller Structure

1. Use `@RestController` and `@RequestMapping("${input:basePath}")`
2. Inject the service using constructor injection
3. Include proper OpenAPI/Swagger annotations
4. Use `ResponseEntity` for all responses

### Endpoints to Generate

| Method | Path | Description | Response Code |
|--------|------|-------------|---------------|
| GET | / | List all (paginated) | 200 |
| GET | /{id} | Get by ID | 200, 404 |
| POST | / | Create new | 201 |
| PUT | /{id} | Update existing | 200, 404 |
| DELETE | /{id} | Delete by ID | 204, 404 |

### Implementation Details

```java
@RestController
@RequestMapping("${input:basePath}")
@RequiredArgsConstructor
@Tag(name = "${input:entityName}", description = "${input:entityName} management endpoints")
@Validated
public class ${input:entityName}Controller {

    private final ${input:entityName}Service service;

    @GetMapping
    @Operation(summary = "List all ${input:entityName}s with pagination")
    public ResponseEntity<Page<${input:entityName}DTO>> findAll(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "id") String sortBy) {
        // Implementation
    }

    @GetMapping("/{id}")
    @Operation(summary = "Get ${input:entityName} by ID")
    public ResponseEntity<${input:entityName}DTO> findById(
            @PathVariable Long id) {
        // Implementation
    }

    @PostMapping
    @Operation(summary = "Create a new ${input:entityName}")
    public ResponseEntity<${input:entityName}DTO> create(
            @Valid @RequestBody Create${input:entityName}Request request) {
        // Implementation - return 201 Created
    }

    @PutMapping("/{id}")
    @Operation(summary = "Update an existing ${input:entityName}")
    public ResponseEntity<${input:entityName}DTO> update(
            @PathVariable Long id,
            @Valid @RequestBody Update${input:entityName}Request request) {
        // Implementation
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "Delete a ${input:entityName}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        // Implementation - return 204 No Content
    }
}
```

### Additional Requirements

1. Follow the coding standards in [java.instructions.md](java.instructions.md)
2. Add proper logging for all operations
3. Include correlation ID header handling
4. Create corresponding DTO classes if they don't exist
5. Create request classes (Create, Update) with validation

### Output Files

Create the following files:
1. `src/main/java/${input:package}/controller/${input:entityName}Controller.java`
2. `src/main/java/${input:package}/model/dto/${input:entityName}DTO.java` (if not exists)
3. `src/main/java/${input:package}/model/dto/Create${input:entityName}Request.java`
4. `src/main/java/${input:package}/model/dto/Update${input:entityName}Request.java`

After generation, run: `mvn compile` to verify the code compiles successfully.
