---
layout: default
title: Spring Boot Quickstart
prev_page: /
# next_page: /docs/02-rest-api
---

# 🏁 Create a Minimal Spring Boot Project (macOS & WSL Friendly)

Welcome to your first Spring Boot mission! Let’s go from zero to a running app in less than 10 minutes, using **VS Code** and **Spring Initializr**—on both **macOS** and **WSL (Windows/Linux)**.

---

## 🧰 What You Need First

* ✅ **Java 17+ (JDK)**

  * macOS: Install via [Homebrew](https://brew.sh): `brew install openjdk@17`
  * WSL: Use SDKMAN: `curl -s "https://get.sdkman.io" | bash`, then `sdk install java 17`
* ✅ **Maven**

  * macOS: `brew install maven`
  * WSL: `sudo apt install maven`
* ✅ **VS Code**

  * Install from: [https://code.visualstudio.com](https://code.visualstudio.com)
* ✅ **VS Code Extensions**

  * Java Extension Pack (by Microsoft)
  * Spring Boot Extension Pack (optional)

---

## 🛠 Step-by-Step: Build & Run Your First Project

### 1️⃣ Use Spring Initializr from VS Code

1. Open **VS Code**

2. Open Command Palette (`⇧⌘P` on macOS, `Ctrl+Shift+P` on Windows/WSL)

3. Type `Spring Initializr: Generate` and select it

4. Fill in these values when prompted:

   * **Project Type:** Maven
   * **Language:** Java
   * **Spring Boot Version:** 3.2.x (or latest)
   * **Group:** `com.example`
   * **Artifact:** `demo`
   * **Name:** `demo`
   * **Package Name:** `com.example.demo`
   * **Dependencies:** `Spring Web`

5. Choose a folder to save the generated project

6. VS Code will automatically prompt you to open the project — click **Open**

---

### 2️⃣ Open & Trust the Project

If prompted:

* Click **Yes** to trust the authors of the files.
* Let VS Code auto-import Maven dependencies.
* Make sure Java is correctly configured (check the bottom-right corner or open terminal and run `java -version`).

---

### 3️⃣ Run the Application

1. In the **Explorer**, open:
   `src/main/java/com/example/demo/DemoApplication.java`

2. The file should look like this:

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
```

3. Click the green **Run** button at the top or right-click inside the editor and select **Run Java**.

---

### ✅ Test It!

Once running, open your browser and go to:

```
http://localhost:8080
```

You’ll see a 404 page — that’s expected. You haven’t added any endpoints yet.

Next step: you’ll build your first REST API.

---

Let me know if you'd like to add `curl`/CLI instructions, Git integration, or REST API examples in the next section!
