---
layout: default
title: Database with JPA
prev_page: /docs/04-validation-error-handling
next_page: /docs/06-testing
---

# 🛢️ Chapter 5: Database Integration with Spring Data JPA

Now that your Todo app has a clean architecture with a service and repository layer, it's time to connect it to a **real database** using **Spring Data JPA** and **PostgreSQL**.

---

## 🚀 What You'll Build

- A `Todo` entity mapped to a `todos` database table
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

## ⚙️ Step 2: Configure `application.properties`

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/todo_db
spring.datasource.username=todo_user
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

---

## 🧠 Step 3: Define JPA Entity

### Todo Entity (Simplified, No User Reference)

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

    // Getters and Setters
}
```

This version removes the `User` entity to keep the design focused and simple. You can add `User` later if authentication or multi-user support is needed.

---

## 📂 Repository Layer

```java
@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
    // You don't need to declare common methods like findAll, findById, save, deleteById
    // Spring Data JPA provides them out of the box.
}
```

### ❓ Why so few methods?

Although this interface appears empty, it inherits many powerful methods from `JpaRepository<Todo, Long>`, such as:

- `findAll()`
- `findById(Long id)`
- `save(Todo todo)`
- `deleteById(Long id)`

Spring Data JPA dynamically implements these using method name conventions and proxy objects.

---

## 🧪 Service Layer

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

## 🌐 Controller Layer

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
- Defined a `Todo` JPA entity without user dependency
- Configured Docker for database setup
- Tested everything using `curl`

Next: [Testing & Coverage ➡️](/docs/06-testing)
