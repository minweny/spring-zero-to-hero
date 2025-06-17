---
layout: default
title: Service Layer Architecture
prev_page: /docs/02-rest-api
next_page: /docs/04-validation-error-handling
---

# 🧠 Add a Service Layer (Clean Code 101)

Your REST API works — but right now, **everything lives in the controller**. As your app grows, that gets messy fast.

Let’s refactor your code to follow a **professional 3-layer architecture**:

* **Controller**: Handles HTTP requests and responses
* **Service**: Contains business logic
* **Repository**: Manages data access (in-memory for now, ready for DB later)

---

## 🧱 What You’ll Build

* Separate concerns for better maintainability
* Use **constructor-based Dependency Injection** for wiring components
* Prepare your app to easily swap in a database later

---

## 🧠 Step 1: Create the Repository Layer

The repository will abstract data access. For now, it’s an in-memory store.

Create interface:

**`TodoRepository.java`**

```java
package com.example.demo.repository;

import com.example.demo.Todo;

import java.util.List;
import java.util.Optional;

public interface TodoRepository {
    List<Todo> findAll();
    Optional<Todo> findById(Long id);
    Todo save(Todo todo);
    void deleteById(Long id);
}
```

Then implement it:

**`TodoRepositoryImpl.java`**

```java
package com.example.demo.repository;

import com.example.demo.Todo;
import org.springframework.stereotype.Repository;

import java.util.*;

@Repository  // Marks this class as a Spring-managed component (bean)
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

## 🧠 Step 2: Create the Service Layer

The service will contain business logic and use the repository to access data.

**Dependency Injection explained:**
We use **constructor-based injection** here to let Spring automatically provide the repository instance to the service. This makes the service easier to test and maintain because dependencies are explicit and immutable.

Create interface:

**`TodoService.java`**

```java
package com.example.demo.service;

import com.example.demo.Todo;

import java.util.List;

public interface TodoService {
    List<Todo> findAll();
    Todo findById(Long id);
    Todo create(Todo todo);
    Todo update(Long id, Todo todo);
    void delete(Long id);
}
```

Implement the service:

**`TodoServiceImpl.java`**

```java
package com.example.demo.service;

import com.example.demo.Todo;
import com.example.demo.repository.TodoRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.NoSuchElementException;

@Service  // Marks this as a Spring-managed service bean
public class TodoServiceImpl implements TodoService {

    private final TodoRepository todoRepository;

    // Constructor-based Dependency Injection:
    // Spring injects the TodoRepository bean automatically when creating this service
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
            .orElseThrow(() -> new NoSuchElementException("Todo not found with ID: " + id));
    }

    @Override
    public Todo create(Todo todo) {
        return todoRepository.save(todo);
    }

    @Override
    public Todo update(Long id, Todo updatedTodo) {
        Todo existingTodo = findById(id);
        updatedTodo.setId(existingTodo.getId());
        return todoRepository.save(updatedTodo);
    }

    @Override
    public void delete(Long id) {
        findById(id); // Verify exists, else exception
        todoRepository.deleteById(id);
    }
}
```

---

## 🧠 Step 3: Update Your Controller

The controller now depends on the **service** rather than handling business logic or data access directly.

**Dependency Injection here:**
We inject the `TodoService` into the controller via its constructor, letting Spring handle the wiring.

Update **`TodoController.java`**

```java
package com.example.demo.controller;

import com.example.demo.Todo;
import com.example.demo.service.TodoService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/todos")
public class TodoController {

    private final TodoService todoService;

    // Constructor injection of the service bean
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

## ✅ Summary

* You introduced a **3-layer architecture**: Controller, Service, Repository
* You used **constructor-based dependency injection** to decouple layers and improve testability
* Business logic moved cleanly into the **service layer**
* Data access is encapsulated in the **repository layer**
* Controller became thin and focused only on HTTP concerns
