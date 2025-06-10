---
layout: default
title: Service Layer Architecture
prev_page: /docs/02-rest-api
next_page: /docs/04-jpa-db
---

# 🧠 Add a Service Layer (Clean Code 101)

Your REST API works — but right now, **everything lives in the controller**. As your app grows, that gets messy fast.

Let’s refactor your code to follow a **layered architecture**:

* `Controller`: Handles HTTP
* `Service`: Handles business logic
* (Coming soon) `Repository`: Handles database access

---

## 🧱 What You'll Do

* Move core logic from controller → service
* Apply `@Service` and `@Autowired`
* Keep your controller thin and clean

💡 This is a **crucial** refactor for real-world apps. You’ll write maintainable code that's easier to test and debug.

---

## 🧠 Step 1: Create `TodoService`

In `com.example.demo`, create:

**`TodoService.java`**

```java
package com.example.demo;

import org.springframework.stereotype.Service;

import java.util.*;

@Service
public class TodoService {

    private final Map<Long, Todo> todos = new HashMap<>();
    private long nextId = 1;

    public List<Todo> findAll() {
        return new ArrayList<>(todos.values());
    }

    public Todo findById(Long id) {
        Todo todo = todos.get(id);
        if (todo == null) {
            throw new NoSuchElementException("Todo not found with ID: " + id);
        }
        return todo;
    }

    public Todo create(Todo newTodo) {
        newTodo.setId(nextId++);
        todos.put(newTodo.getId(), newTodo);
        return newTodo;
    }

    public Todo update(Long id, Todo updatedTodo) {
        if (!todos.containsKey(id)) {
            throw new NoSuchElementException("Todo not found with ID: " + id);
        }
        updatedTodo.setId(id);
        todos.put(id, updatedTodo);
        return updatedTodo;
    }

    public void delete(Long id) {
        if (!todos.containsKey(id)) {
            throw new NoSuchElementException("Todo not found with ID: " + id);
        }
        todos.remove(id);
    }
}
```

---

## 🔧 Step 2: Update Your `TodoController`

Refactor the controller to use the service:

**`TodoController.java`**

```java
package com.example.demo;

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

    @GetMapping
    public ResponseEntity<List<Todo>> getAllTodos() {
        return ResponseEntity.ok(todoService.findAll());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Todo> getTodoById(@PathVariable Long id) {
        return ResponseEntity.ok(todoService.findById(id));
    }

    @PostMapping
    public ResponseEntity<Todo> createTodo(@RequestBody Todo newTodo) {
        return ResponseEntity.ok(todoService.create(newTodo));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Todo> updateTodo(@PathVariable Long id, @RequestBody Todo updatedTodo) {
        return ResponseEntity.ok(todoService.update(id, updatedTodo));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTodo(@PathVariable Long id) {
        todoService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

## 🚨 Step 3: Improve Error Handling

Update your global exception handler to support `NoSuchElementException`:

**`GlobalExceptionHandler.java`**

```java
package com.example.demo;

import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;

import java.util.NoSuchElementException;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NoSuchElementException.class)
    public ResponseEntity<String> handleNotFound(NoSuchElementException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneric(Exception ex) {
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body("Something went wrong: " + ex.getMessage());
    }
}
```

---

## 🧪 Step 4: Test Again

Your endpoints work **exactly the same** as before.

```bash
curl http://localhost:8080/api/todos
```

But now your code is **much more maintainable** — and you're ready to plug in a database.

---

## ✅ Summary

* You introduced a **Service Layer**
* You used `@Service` and **constructor injection**
* Your controller is now clean and focused
* Logic is centralized and easy to test

---

### ➡️ Next: [04. Connect to a Real Database →](/docs/04-jpa-db)

You’ll replace your in-memory `Map<Long, Todo>` with a real relational database using Spring Data JPA.
