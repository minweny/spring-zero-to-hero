---
layout: default
title: Database with JPA
prev_page: /docs/04-validation-error-handling
# next_page: /docs/06-testing
---

# 🛢️ Chapter 5: Database Integration with Spring Data JPA

Now that your Todo app has a clean architecture with a service and repository layer, it's time to connect it to a **real database** using **Spring Data JPA** and **PostgreSQL**.

---

## 🚀 What You'll Build

- A `Todo` entity mapped to a `todos` database table
- A `User` entity with relationship to Todos
- A Spring Data JPA `TodoRepository`
- Integration with PostgreSQL via Docker
- Ability to query and persist data from a real database

---

## 🧱 Step 1: Set Up PostgreSQL via Docker

Create a `docker-compose.yml`:

```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    container_name: todo-postgres
    environment:
      POSTGRES_USER: todo_user
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: todo_db
    ports:
      - '5432:5432'
```

Run:

```bash
docker-compose up -d
```

---

## 🧠 Step 2: Configure `application.properties`

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/todo_db
spring.datasource.username=todo_user
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

---

## 🧠 Step 3: Define the Entity Class

```java
@Entity
@Table(name = "todos")
public class Todo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    private String description;

    @Column(nullable = false)
    private boolean completed;

    private Instant createdAt = Instant.now();

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    // Getters and Setters
}
```

---

## 📂 Repository Layer

```java
@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
    List<Todo> findByUserIdOrderByCreatedAtDesc(Long userId);

    @Query("SELECT t FROM Todo t WHERE t.user.id = :userId AND t.completed = :completed")
    List<Todo> findByUserIdAndCompleted(@Param("userId") Long userId, @Param("completed") boolean completed);
}
```

### ❓ Why so few methods?

Although this interface only defines 2 custom methods, it inherits many useful ones from `JpaRepository<Todo, Long>`, such as:

- `findAll()`
- `findById(Long id)`
- `save(Todo todo)`
- `deleteById(Long id)`

These are auto-implemented by Spring at runtime using reflection. You get them for free just by extending `JpaRepository`.

---

## ⚙️ Service Layer (Update)

```java
@Service
public class TodoServiceImpl implements TodoService {
    private final TodoRepository todoRepository;

    @Autowired
    public TodoServiceImpl(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    public List<Todo> findAll() {
        return todoRepository.findAll();
    }

    public Todo findById(Long id) {
        return todoRepository.findById(id).orElseThrow();
    }

    public Todo create(Todo todo) {
        return todoRepository.save(todo);
    }

    public Todo update(Long id, Todo updatedTodo) {
        Todo existing = findById(id);
        updatedTodo.setId(existing.getId());
        return todoRepository.save(updatedTodo);
    }

    public void delete(Long id) {
        todoRepository.deleteById(id);
    }
}
```

---

## 📡 Controller Layer (No Change)

```java
@RestController
@RequestMapping("/api/todos")
public class TodoController {
    private final TodoService todoService;

    public TodoController(TodoService todoService) {
        this.todoService = todoService;
    }

    @GetMapping
    public ResponseEntity<List<Todo>> getAllTodos() {
        return ResponseEntity.ok(todoService.findAll());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Todo> getTodoById(@PathVariable Long id) {
        return ResponseEntity.ok(todoService.findById(id));
    }

    @PostMapping
    public ResponseEntity<Todo> createTodo(@RequestBody Todo todo) {
        return ResponseEntity.ok(todoService.create(todo));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Todo> updateTodo(@PathVariable Long id, @RequestBody Todo todo) {
        return ResponseEntity.ok(todoService.update(id, todo));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTodo(@PathVariable Long id) {
        todoService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

## ✅ Test with curl

```bash
# Create
curl -X POST -H "Content-Type: application/json" \
    -d '{"title":"Buy milk","description":"2% organic","completed":false}' \
    http://localhost:8080/api/todos

# Get all
curl http://localhost:8080/api/todos

# Update
curl -X PUT -H "Content-Type: application/json" \
    -d '{"title":"Buy almond milk","description":"Unsweetened","completed":true}' \
    http://localhost:8080/api/todos/1

# Delete
curl -X DELETE http://localhost:8080/api/todos/1
```

---

## ✅ Summary

- Connected your app to PostgreSQL using Spring Data JPA
- Used `JpaRepository` to simplify data access
- Defined `Todo` as a JPA entity with relationships
- Added `docker-compose` for local database
- Tested everything via `curl`

Next: [Testing & Coverage ➡️](/docs/06-testing)
