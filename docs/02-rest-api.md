---
layout: default
title: REST API Development
prev_page: /docs/01-spring-boot-quickstart
next_page: /docs/03-service-layer
---

# 🌐 Build a Todo REST API 🔥🔥🔥

Let’s take your Spring Boot skills to the next level by building a **fully functional REST API** — no frontend, no database, just clean, JSON-speaking HTTP endpoints using in-memory data.

---

## ✅ What You’ll Build

A Todo API with endpoints like:

```
GET    /api/todos         → list all todos  
GET    /api/todos/{id}    → get a single todo  
POST   /api/todos         → create a new todo  
PUT    /api/todos/{id}    → update an existing todo  
DELETE /api/todos/{id}    → delete a todo
```

---

## 🛠️ Step 1: Create a `Todo` Model

In `src/main/java/com/example/demo/`, create:

**`Todo.java`**

```java
package com.example.demo;

public class Todo {
    private Long id;
    private String title;
    private boolean completed;

    public Todo() {}
    public Todo(Long id, String title, boolean completed) {
        this.id = id;
        this.title = title;
        this.completed = completed;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public boolean isCompleted() { return completed; }
    public void setCompleted(boolean completed) { this.completed = completed; }
}
```

---

## 🛠️ Step 2: Create the `TodoController`

Add your REST API endpoints:

**`TodoController.java`**

```java
package com.example.demo;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.*;

@RestController
@RequestMapping("/api/todos")
public class TodoController {

    private final Map<Long, Todo> todos = new HashMap<>();
    private long nextId = 1;

    @GetMapping
    public ResponseEntity<List<Todo>> getAllTodos() {
        return ResponseEntity.ok(new ArrayList<>(todos.values()));
    }

    @GetMapping("/{id}")
    public ResponseEntity<Todo> getTodoById(@PathVariable Long id) {
        Todo todo = todos.get(id);
        return todo != null ? ResponseEntity.ok(todo)
                            : ResponseEntity.notFound().build();
    }

    @PostMapping
    public ResponseEntity<Todo> createTodo(@RequestBody Todo newTodo) {
        newTodo.setId(nextId++);
        todos.put(newTodo.getId(), newTodo);
        return ResponseEntity.ok(newTodo);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Todo> updateTodo(@PathVariable Long id, @RequestBody Todo updatedTodo) {
        if (!todos.containsKey(id)) {
            return ResponseEntity.notFound().build();
        }
        updatedTodo.setId(id);
        todos.put(id, updatedTodo);
        return ResponseEntity.ok(updatedTodo);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTodo(@PathVariable Long id) {
        return todos.remove(id) != null
            ? ResponseEntity.noContent().build()
            : ResponseEntity.notFound().build();
    }
}
```

---

## 🚨 Step 3: Add Global Error Handling (Optional)

Create:

**`GlobalExceptionHandler.java`**

```java
package com.example.demo;

import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body("Something went wrong: " + ex.getMessage());
    }
}
```

---

## 🧪 Step 4: Test Your API

Use **curl**, **Postman**, or **VS Code REST Client**.

### ✅ Create a Todo

```bash
curl -X POST http://localhost:8080/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Write guide", "completed": false}'
```

### 📋 Get All Todos

```bash
curl http://localhost:8080/api/todos
```

### ✏️ Update a Todo

```bash
curl -X PUT http://localhost:8080/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Finish REST guide", "completed": true}'
```

### ❌ Delete a Todo

```bash
curl -X DELETE http://localhost:8080/api/todos/1
```

---

## 💡 Outcome

* You created a **real REST API**
* You used `@RestController`, `@PathVariable`, `@RequestBody`, and `ResponseEntity`
* You stored and managed data **in memory** — no database needed
* You saw how Spring Boot handles JSON out of the box

---

### ➡️ Next: [03. Layered Architecture →](/docs/03-service-layer)

Now let’s clean things up. We'll move logic into a **Service Layer** to make our code scalable and maintainable.
