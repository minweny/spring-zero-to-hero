---
layout: default
title: Spring Boot Quickstart
prev_page: /
next_page: /docs/02-rest-api
---

# 🏁 Spring Boot Quickstart 🔥🔥🔥

Welcome to your first Spring Boot mission! You’ll go from zero to a running application in **under 5 minutes**, using **VS Code** and **Spring Initializr**. This guide works on **macOS**, **Windows**, and **WSL (Linux)**.

---

## 🧰 What You’ll Need

Before we begin, make sure you have the following tools installed:

### ✅ Java 17+

* **macOS**:

  ```bash
  brew install openjdk@17
  ```

  Add it to your shell config:

  ```bash
  export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"
  ```

* **WSL (Ubuntu/Debian)**:
  Use SDKMAN:

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

* Download from: [https://code.visualstudio.com](https://code.visualstudio.com)
* Recommended Extensions:

  * Java Extension Pack (by Microsoft)
  * Spring Boot Extension Pack (optional)

---

## 🛠️ Step-by-Step: Build Your First Spring Boot Project

### 1️⃣ Generate Project via Spring Initializr

You can do this **inside VS Code** or through the **web**.

#### 📦 Option A: Use VS Code

1. Open **Command Palette**:

   * macOS: `⇧⌘P`
   * Windows/WSL: `Ctrl+Shift+P`

2. Type and select:
   `Spring Initializr: Generate a Maven Project`

3. Fill in these values:

   * **Language**: Java
   * **Spring Boot Version**: 3.2.x (or latest)
   * **Group**: `com.example`
   * **Artifact**: `demo`
   * **Name**: `demo`
   * **Package Name**: `com.example.demo`
   * **Dependencies**: `Spring Web`

4. Choose a folder to save the project. VS Code will prompt to open it — click **Open**.

#### 📦 Option B: Use the Website

1. Go to [https://start.spring.io](https://start.spring.io)
2. Fill in the same values as above
3. Click **Generate**, then unzip the project and open it in VS Code

---

### 2️⃣ Explore the Project Structure

Your project will include:

```
demo/
├── src/
│   └── main/
│       ├── java/com/example/demo/DemoApplication.java
│       └── resources/application.properties
├── pom.xml
```

Key files:

* `DemoApplication.java`: Your app's entry point
* `application.properties`: Where you configure behavior (like port, logging)
* `pom.xml`: Maven configuration (dependencies, plugins)

---

### 3️⃣ Run the Application

You have two options:

* **VS Code**: Click the green “Run” button at the top of `DemoApplication.java`
* **Terminal**:

  ```bash
  mvn spring-boot:run
  ```

Output:

```text
Tomcat started on port 8080 (http)...
Started DemoApplication in 2.123 seconds
```

---

### 4️⃣ Open in Browser

Visit:

```
http://localhost:8080
```

You’ll see a **Whitelabel Error Page** — that's expected. You haven’t added any controllers yet!

---

### 5️⃣ Customize `application.properties`

Edit this file:

```properties
server.port=9090
spring.application.name=demo-app
```

Re-run your app and visit:

```
http://localhost:9090
```

🎉 You’ve just customized your app's behavior!

---

## ✅ Outcome

* You created and ran a Spring Boot app in under 5 minutes
* You explored the project structure
* You modified application settings and verified changes live

---

Next up: [Build Your First REST API →](/docs/02-rest-api)

---

Let me know if you want to add Git integration, REST endpoints, or Docker setup in the next chapter!
