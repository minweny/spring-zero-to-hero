---
layout: default
title: Spring Boot Quickstart
prev_page: /
next_page: /docs/02-rest-api
---

# 🏁 Spring Boot Quickstart 🔥🔥🔥

**Your first steps into the Spring ecosystem, creating the foundation of our Todo application.**
⏱️ *Time: 30 mins*

---

## 🚀 What You'll Build

* A Todo project scaffolded with Spring Initializr
* Your first Spring Boot app that says: `Hello, Todo!`
* A working understanding of Spring Boot’s auto-configuration
* A REST endpoint, served locally in your browser

---

## 📘 Key Concepts

* Spring Boot project structure
* `@SpringBootApplication` – the main entry point
* `@RestController` – define simple APIs
* `application.properties` – basic configuration

---

## 🧰 Prerequisites

Install the following:

### ✅ Java 17+

* **macOS**:

  ```bash
  brew install openjdk@17
  export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"
  ```

* **WSL (Ubuntu/Debian)**:

  ```bash
  curl -s "https://get.sdkman.io" | bash
  source "$HOME/.sdkman/bin/sdkman-init.sh"
  sdk install java 17
  ```

---

### ✅ Maven

* **macOS**:

  ```bash
  brew install maven
  ```

* **WSL**:

  ```bash
  sudo apt update
  sudo apt install maven
  ```

---

### ✅ VS Code

* Download: [https://code.visualstudio.com](https://code.visualstudio.com)
* Extensions:

  * Java Extension Pack ✅
  * Spring Boot Extension Pack (optional)

---

## 🛠️ Hands-on: Build the Todo App

### 1️⃣ Create a Project (Spring Initializr)

#### 📦 Option A: Inside VS Code

1. Open Command Palette:

   * macOS: `⇧⌘P`
   * Windows/WSL: `Ctrl+Shift+P`

2. Run: `Spring Initializr: Generate a Maven Project`

3. Fill in:

   * Language: Java
   * Spring Boot Version: 3.2.x
   * Group: `com.example`
   * Artifact/Name: `demo`
   * Package: `com.example.demo`
   * Dependency: `Spring Web`

4. Choose folder → Open in VS Code

#### 📦 Option B: [start.spring.io](https://start.spring.io)

* Same values as above
* Download and unzip → Open in VS Code

---

### 2️⃣ Understand the Structure

```bash
demo/
├── src/main/java/com/example/demo/DemoApplication.java
├── src/main/resources/application.properties
└── pom.xml
```

* `DemoApplication.java` – Main class
* `application.properties` – Config
* `pom.xml` – Maven dependencies

---

### 3️⃣ Add Your First Endpoint

Create `TodoController.java`:

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TodoController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello, Todo!";
    }
}
```

---

### 4️⃣ Run the App

In **VS Code**:
Click ▶️ on `DemoApplication.java`
Or use Terminal:

```bash
mvn spring-boot:run
```

Output should say:
`Tomcat started on port 8080`

---

### 5️⃣ Test in Your Browser

Visit:

```
http://localhost:8080/hello
```

✅ You’ll see: `Hello, Todo!`

---

### 6️⃣ Configure Your App

Edit `src/main/resources/application.properties`:

```properties
server.port=9090
spring.application.name=todo-app
```

Restart the app. Now visit:

```
http://localhost:9090/hello
```

---

## ✅ Outcome

By now, you've:

* Created a Spring Boot project
* Built and tested your first REST API
* Modified application settings
