---
layout: default
title: Todo API with Data Validation & Error Handling
prev_page: /docs/03-service-layer
# next_page: /docs/04-jpa-db
---

# Chapter 4: Todo API with Data Validation & Error Handling (Full Guide)

---

## Overview

In this chapter, we will build a **Todo API** that:

* Validates incoming data to ensure correctness
* Handles errors globally and returns user-friendly error messages
* Uses a simple in-memory repository to keep things focused on validation & error handling

---

## Concepts Covered

* **Bean Validation (JSR 380):** Use annotations like `@NotBlank` and `@Size` to validate input data automatically.
* **DTO (Data Transfer Object):** Separate input data from your model/entity for better control.
* **Custom Exceptions:** Create exceptions for business rules (like "Todo not found").
* **Global Exception Handling:** Use `@ControllerAdvice` to handle exceptions in one place.
* **ResponseEntity:** Standard way to build HTTP responses with status codes and bodies.

---

# 1. Project Structure

```
src
└── main
    └── java
        └── com.example.todo
            ├── TodoApplication.java
            ├── controller
            │   └── TodoController.java
            ├── dto
            │   └── TodoRequest.java
            ├── exception
            │   ├── GlobalExceptionHandler.java
            │   └── TodoNotFoundException.java
            ├── model
            │   └── Todo.java
            ├── repository
            │   ├── TodoRepository.java
            │   └── TodoRepositoryImpl.java
            └── service
                ├── TodoService.java
                └── TodoServiceImpl.java
```

---

# 2. Source Code + Explanations

---

### 2.1 `TodoApplication.java`

This is the Spring Boot entry point.

```java
package com.example.todo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class TodoApplication {
    public static void main(String[] args) {
        SpringApplication.run(TodoApplication.class, args);
    }
}
```

---

### 2.2 Model: `Todo.java`

Represents the Todo item in memory.
It contains: `id`, `title`, `description`, and `completed` status.

```java
package com.example.todo.model;

public class Todo {
    private Long id;
    private String title;
    private String description;
    private Boolean completed;

    // Constructors
    public Todo() {}
    public Todo(Long id, String title, String description, Boolean completed) {
        this.id = id;
        this.title = title;
        this.description = description;
        this.completed = completed;
    }

    // Getters and setters (for serialization/deserialization)
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public Boolean getCompleted() { return completed; }
    public void setCompleted(Boolean completed) { this.completed = completed; }
}
```

---

### 2.3 DTO (Data Transfer Object): `TodoRequest.java`

This class is used to receive user input for creating or updating Todos.

We use **Bean Validation annotations** here:

* `@NotBlank` — the `title` must be provided and not just empty spaces.
* `@Size` — limit max length of title and description.
* `@NotNull` — `completed` must be true or false (not null).

```java
package com.example.todo.dto;

import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;

public class TodoRequest {

    @NotBlank(message = "Title is required")
    @Size(max = 100, message = "Title must be at most 100 characters")
    private String title;

    @Size(max = 500, message = "Description cannot exceed 500 characters")
    private String description;

    @NotNull(message = "Completion status is required")
    private Boolean completed;

    // Getters and setters
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public Boolean getCompleted() { return completed; }
    public void setCompleted(Boolean completed) { this.completed = completed; }
}
```

---

### 2.4 Repository Interface: `TodoRepository.java`

Defines basic data operations. Here it's an interface so you can switch implementations later.

```java
package com.example.todo.repository;

import com.example.todo.model.Todo;

import java.util.List;
import java.util.Optional;

public interface TodoRepository {
    List<Todo> findAll();
    Optional<Todo> findById(Long id);
    Todo save(Todo todo);
    void deleteById(Long id);
}
```

---

### 2.5 Repository Implementation: `TodoRepositoryImpl.java`

Simple in-memory storage using a `HashMap` and auto-increment ID.

```java
package com.example.todo.repository;

import com.example.todo.model.Todo;
import org.springframework.stereotype.Repository;

import java.util.*;

@Repository
public class TodoRepositoryImpl implements TodoRepository {

    private final Map<Long, Todo> todos = new HashMap<>();
    private long nextId = 1;

    @Override
    public List<Todo> findAll() {
        return new ArrayList<>(todos.values());
    }

    @Override
    public Optional<Todo> findById(Long id) {
        return Optional.ofNullable(todos.get(id));
    }

    @Override
    public Todo save(Todo todo) {
        if (todo.getId() == null) {
            todo.setId(nextId++);
        }
        todos.put(todo.getId(), todo);
        return todo;
    }

    @Override
    public void deleteById(Long id) {
        todos.remove(id);
    }
}
```

---

### 2.6 Service Interface: `TodoService.java`

Defines business logic methods.

```java
package com.example.todo.service;

import com.example.todo.model.Todo;

import java.util.List;

public interface TodoService {
    List<Todo> findAll();
    Todo findById(Long id);
    Todo create(Todo todo);
    Todo update(Long id, Todo todo);
    void delete(Long id);
}
```

---

### 2.7 Service Implementation: `TodoServiceImpl.java`

Implements business logic and throws exceptions when needed.

```java
package com.example.todo.service;

import com.example.todo.exception.TodoNotFoundException;
import com.example.todo.model.Todo;
import com.example.todo.repository.TodoRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class TodoServiceImpl implements TodoService {

    private final TodoRepository todoRepository;

    @Autowired
    public TodoServiceImpl(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @Override
    public List<Todo> findAll() {
        return todoRepository.findAll();
    }

    @Override
    public Todo findById(Long id) {
        return todoRepository.findById(id)
                .orElseThrow(() -> new TodoNotFoundException(id));
    }

    @Override
    public Todo create(Todo todo) {
        return todoRepository.save(todo);
    }

    @Override
    public Todo update(Long id, Todo updatedTodo) {
        Todo existing = findById(id); // Throws if not found
        updatedTodo.setId(existing.getId());
        return todoRepository.save(updatedTodo);
    }

    @Override
    public void delete(Long id) {
        findById(id); // Throws if not found
        todoRepository.deleteById(id);
    }
}
```

---

### 2.8 Custom Exception: `TodoNotFoundException.java`

This exception is thrown when a requested Todo item is not found.

```java
package com.example.todo.exception;

public class TodoNotFoundException extends RuntimeException {
    public TodoNotFoundException(Long id) {
        super("Todo not found with ID: " + id);
    }
}
```

---

### 2.9 Global Exception Handler: `GlobalExceptionHandler.java`

* Handles validation errors (`MethodArgumentNotValidException`) from `@Valid`.
* Handles `TodoNotFoundException`.
* Returns JSON error messages with proper HTTP status codes.

```java
package com.example.todo.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class GlobalExceptionHandler {

    // Handles validation errors and returns a map of field -> error message
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Object> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();

        for (FieldError error : ex.getBindingResult().getFieldErrors()) {
            errors.put(error.getField(), error.getDefaultMessage());
        }

        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }

    // Handles custom "not found" exception
    @ExceptionHandler(TodoNotFoundException.class)
    public ResponseEntity<Object> handleTodoNotFoundException(TodoNotFoundException ex) {
        Map<String, String> error = Map.of("error", ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    // You can add more exception handlers here for other types of errors.
}
```

---

### 2.10 Controller: `TodoController.java`

* Accepts JSON requests
* Validates input using `@Valid` and `TodoRequest` DTO
* Converts DTO to `Todo` model internally
* Calls service layer for CRUD operations
* Returns JSON responses and status codes

```java
package com.example.todo.controller;

import com.example.todo.dto.TodoRequest;
import com.example.todo.model.Todo;
import com.example.todo.service.TodoService;
import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/todos")
public class TodoController {

    private final TodoService todoService;

    public TodoController(TodoService todoService) {
        this.todoService = todoService;
    }

    // Convert DTO to model entity
    private Todo convertToEntity(TodoRequest request) {
        Todo todo = new Todo();
        todo.setTitle(request.getTitle());
        todo.setDescription(request.getDescription());
        todo.setCompleted(request.getCompleted());
        return todo;
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
    public ResponseEntity<Todo> createTodo(@Valid @RequestBody TodoRequest todoRequest) {
        Todo todo = convertToEntity(todoRequest);
        Todo created = todoService.create(todo);
        return ResponseEntity.ok(created);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Todo> updateTodo(
            @PathVariable Long id,
            @Valid @RequestBody TodoRequest todoRequest) {
        Todo todo = convertToEntity(todoRequest);
        Todo updated = todoService.update(id, todo);
        return ResponseEntity.ok(updated);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTodo(@PathVariable Long id) {
        todoService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

# 3. Maven Dependencies (`pom.xml`)

Make sure you include the validation starter and web starter:

```xml
<dependencies>
    <!-- Web starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Validation starter for @Valid, @NotBlank, etc -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
</dependencies>
```

---

# 4. Run the Application

* Use your IDE or command line (`mvn spring-boot:run`).
* The server starts at `http://localhost:8080`.

---

# 5. Testing with curl

### 5.1 Create Todo (Valid)

```bash
curl -X POST http://localhost:8080/api/todos \
-H "Content-Type: application/json" \
-d '{"title": "Learn Spring Boot", "description": "Finish validation chapter", "completed": false}'
```

Response:

```json
{
  "id": 1,
  "title": "Learn Spring Boot",
  "description": "Finish validation chapter",
  "completed": false
}
```

---

### 5.2 Create Todo (Invalid - empty title)

```bash
curl -X POST http://localhost:8080/api/todos \
-H "Content-Type: application/json" \
-d '{"title": "", "description": "Invalid because title is blank", "completed": false}'
```

Response (HTTP 400):

```json
{
  "title": "Title is required"
}
```

---

### 5.3 Get Todo by ID

```bash
curl http://localhost:8080/api/todos/1
```

If found, returns Todo JSON; if not found, returns:

```json
{
  "error": "Todo not found with ID: 1"
}
```

---

### 5.4 Update Todo

```bash
curl -X PUT http://localhost:8080/api/todos/1 \
-H "Content-Type: application/json" \
-d '{"title": "Learn Spring Boot Updated", "description": "Updated description", "completed": true}'
```

---

### 5.5 Delete Todo

```bash
curl -X DELETE http://localhost:8080/api/todos/1
```

Returns HTTP 204 No Content.

---

# Summary

You now have a complete Spring Boot Todo API with:

* **Input validation** (fail early on bad input)
* **Global error handling** with clear JSON error messages
* **Custom exceptions** for business errors
* **Clean architecture** separating controller, service, repository, and models
* **Easy-to-run and test**
