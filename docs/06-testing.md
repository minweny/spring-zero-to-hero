---
layout: default
title: Testing Fundamentals
prev_page: /docs/05-jpa-db
# next_page: /docs/07-deployment
---

### 6. 🧪 Testing Fundamentals 🔥🔥 (2 hrs)

Ensure the reliability of your Todo application through comprehensive testing.

---

### 📁 Project Structure (Recap)
```
src
├── main
│   ├── java/com/example/todo
│   │   ├── controller/TodoController.java
│   │   ├── model/Todo.java
│   │   ├── repository/TodoRepository.java
│   │   ├── service/TodoService.java
│   │   ├── service/impl/TodoServiceImpl.java
│   │   └── TodoApplication.java
│   └── resources
│       └── application.yml
└── test
    └── java/com/example/todo
        ├── TodoServiceTest.java
        ├── TodoControllerTest.java
        ├── TodoRepositoryTest.java
        └── TodoContainerTest.java
```

---

### 🚀 What You'll Build
- Unit tests for the service layer using JUnit 5 and Mockito
- Integration tests for the repository layer with @DataJpaTest
- API tests for the controller using MockMvc
- Testcontainers to run PostgreSQL during integration tests
- Code coverage reports with Jacoco

---

### 🧠 Key Concepts
- **JUnit 5 & Mockito:** Frameworks for unit testing and mocking dependencies
- **MockMvc:** For testing Spring MVC controllers without starting the server
- **@DataJpaTest:** For lightweight JPA repository testing
- **Testcontainers:** To spin up PostgreSQL in Docker for realistic integration tests
- **Jacoco:** For generating test coverage reports

---

### 🧪 Unit Test: TodoService
```java
@ExtendWith(MockitoExtension.class) // Enables Mockito support in JUnit 5
class TodoServiceTest {

    @Mock // Creates a mock of the repository so we don’t touch the DB
    private TodoRepository todoRepository;

    @InjectMocks // Injects the above mock into TodoServiceImpl
    private TodoServiceImpl todoService;

    @Test
    void createTodo_ShouldReturnSavedTodo() {
        // Arrange: Create a sample Todo
        Todo todo = new Todo("Test Todo", "Description", false);

        // Mock behavior: when save() is called, return the same todo
        when(todoRepository.save(any(Todo.class))).thenReturn(todo);

        // Act: Call the method under test
        Todo result = todoService.create(todo);

        // Assert: Check the returned Todo object
        assertNotNull(result); // Should not be null
        assertEquals("Test Todo", result.getTitle()); // Title should match

        // Verify: Ensure save() was called on the repository
        verify(todoRepository).save(any(Todo.class));
    }
}
```
Explanation:
- `@Mock` simulates `TodoRepository`, meaning it behaves like the real thing but doesn’t access a database.
- `@InjectMocks` lets us test `TodoServiceImpl` as if it’s using the mock repository.
- `when(...).thenReturn(...)` tells Mockito: “If `save` is called with any Todo, return this specific Todo.”
- The test then verifies if the returned Todo is correct and if the `save` method was called.

---

### 🌐 API Test: TodoController
```java
@WebMvcTest(TodoController.class) // Tests only the controller layer
class TodoControllerTest {

    @Autowired
    private MockMvc mockMvc; // Simulates HTTP requests

    @MockBean
    private TodoService todoService; // Mocks the service to isolate controller

    @Test
    void getAllTodos_ShouldReturnTodoList() throws Exception {
        // Arrange: Mock service response
        List<Todo> todos = Arrays.asList(
            new Todo("Todo 1", "", false),
            new Todo("Todo 2", "", true)
        );
        when(todoService.findAll()).thenReturn(todos);

        // Act & Assert: Perform request and verify response
        mockMvc.perform(get("/api/todos"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$", hasSize(2)))
               .andExpect(jsonPath("$[0].title", is("Todo 1")));
    }
}
```

---

### 🧪 Repository Integration Test
```java
@DataJpaTest // Loads only JPA components, fast and isolated
class TodoRepositoryTest {

    @Autowired
    private TodoRepository todoRepository;

    @Test
    void testSaveAndFind() {
        // Save a Todo
        Todo todo = new Todo("Write tests", "Important", false);
        Todo saved = todoRepository.save(todo);

        // Fetch it back
        Optional<Todo> found = todoRepository.findById(saved.getId());

        // Verify it exists and has correct title
        assertTrue(found.isPresent());
        assertEquals("Write tests", found.get().getTitle());
    }
}
```

---

### 🧪 Test with PostgreSQL Using Testcontainers
Add this dependency to `pom.xml`:
```xml
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>postgresql</artifactId>
  <version>1.19.0</version>
  <scope>test</scope>
</dependency>
```

Example test:
```java
@Testcontainers // Enables Testcontainers lifecycle
@DataJpaTest
class TodoContainerTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15")
        .withDatabaseName("todo_db")
        .withUsername("todo_user")
        .withPassword("secret");

    @DynamicPropertySource // Overrides Spring Boot DB properties
    static void overrideProps(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private TodoRepository todoRepository;

    @Test
    void containerDbTest() {
        todoRepository.save(new Todo("Container Todo", "", false));

        List<Todo> todos = todoRepository.findAll();
        assertFalse(todos.isEmpty());
    }
}
```

---

### 📊 Code Coverage with Jacoco
Add to `pom.xml`:
```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.8</version>
  <executions>
    <execution>
      <goals><goal>prepare-agent</goal></goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals><goal>report</goal></goals>
    </execution>
  </executions>
</plugin>
```
Then run:
```bash
mvn test
mvn verify
open target/site/jacoco/index.html
```

---

### ✅ Summary
- Tested service, controller, and repository layers
- Used MockMvc, Mockito, and JUnit for robust test coverage
- Integrated PostgreSQL testing with Testcontainers
- Verified code quality with Jacoco

Next: [Deployment & Docker ➡️](/docs/07-deployment)
