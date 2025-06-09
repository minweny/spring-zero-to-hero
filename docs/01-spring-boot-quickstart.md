---
layout: default
title: Spring Boot Quickstart
prev_page: /
# next_page: /docs/02-rest-api
---

# 🏁 Create a Minimal Project via Spring Initializr

Welcome to your first hands-on Spring Boot mission! Let’s go from 0 to a running app in less than 10 minutes.

---

## 🧰 Tools You'll Need

- Java 17+ (JDK)
- Maven (or Gradle)
- IDE like IntelliJ, VS Code, or Eclipse
- Spring Initializr (https://start.spring.io)

---

## 🛠 Step-by-Step: Build Your First Project

### 1️⃣ Open Spring Initializr

Go to: [https://start.spring.io](https://start.spring.io)

Choose the following:

- **Project:** Maven
- **Language:** Java
- **Spring Boot:** 3.2.x (or latest)
- **Group:** `com.example`
- **Artifact:** `demo`
- **Name:** `demo`
- **Package Name:** `com.example.demo`
- **Dependencies:**
  - Spring Web

Click **Generate** and download the ZIP file.

---

### 2️⃣ Unzip and Open in Your IDE

Unzip the project, open it in your IDE, and let Maven/Gradle import the dependencies.

---

### 3️⃣ Run the Application

In your IDE, locate the file: `DemoApplication.java`

It should look like this:

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
