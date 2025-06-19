---
layout: default
title: Spring Into Action
next_page: /docs/01-spring-boot-quickstart
---

# 🌱 Spring Into Action: From Zero to Hero

Welcome to the least boring Spring Boot tutorial you'll ever read! We'll build a real Todo application from scratch, evolving it from a simple API to a production-ready system.

## 🤔 What's This All About?

Are you tired of tutorials that make Spring Boot sound like rocket science? Well, grab your coffee ☕ (or energy drink, we don't judge), because we're about to turn you from a Spring newbie into a Spring ninja — by actually building things.

## 💡 Why This Book?

There are tons of Spring Boot tutorials out there — but most are either too shallow, too theoretical, or just plain boring.

This book is different:

- 🛠️ **Hands-on, project-first**: We build a real app, step by step.
- 🧠 **Concepts explained as we go**: No long-winded lectures upfront.
- 🎯 **Real-world patterns**: From REST APIs to testing, caching, and deployment.
- 🖥️ **Modern design**: Clean layout, mobile-friendly, dark mode supported.
- 🧹 **Ad-free experience**: No distractions — try disabling your ad blocker!
- 🤓 **Fun and practical**: Clear, modern, and no filler fluff.

Whether you're a student, job-seeker, or dev who wants to finally *get* Spring Boot — this guide is made for you.

## ✅ Prerequisites

- Basic Java knowledge (if you can write "Hello World" without Googling, you're good)
- A sense of humor (very important)
- Coffee (extremely important)

## 📚 Learning Path

### 1. 🏁 Spring Boot Quickstart 🔥🔥🔥 (30 mins)

Your first steps into the Spring ecosystem, creating the foundation of our Todo application.

#### What You'll Build
- Generate a Todo project using Spring Initializr
- Create your first endpoint returning "Hello, Todo!"
- Understand Spring Boot's magic sauce (auto-configuration)
- Run and test your application

#### Key Concepts
- Spring Boot project structure
- Basic application properties
- Entry point annotation (@SpringBootApplication)
- Simple REST endpoint (@RestController)

#### Hands-on Tasks
```java
@RestController
public class TodoController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello, Todo!";
    }
}
```

💡 **Outcome**: A running Spring Boot application with your first endpoint.

---

### 2. 🌐 REST API Development 🔥🔥🔥 (2 hrs)

Build a working REST API with full CRUD operations — all inside the controller — using in-memory data and simple business logic.

#### What You'll Build
- A Todo REST API with endpoints for GET, POST, PUT, DELETE
- In-memory storage using a `HashMap`
- Full controller-driven request handling
- Optional: global error handler

#### Key Concepts
- RESTful endpoint design
- `@RestController`, `@RequestBody`, `@PathVariable`, `ResponseEntity`
- JSON serialization & deserialization
- HTTP method mappings

💡 **Outcome**: A working, self-contained REST API. In the next chapter, we'll break it into services and repositories.

---

### 3. 🧠 Layered Architecture 🔥🔥 (1 hr)

Refactor your Todo API into a **clean, professional 3-layer architecture**.

#### What You'll Build

* A fully decoupled **Controller → Service → Repository** flow
* **Dependency injection** with `@Autowired` and constructor injection
* A **service layer** for business logic
* A **repository layer** for data access (initially in-memory)

#### Project Structure

```
src/main/java/com/example/demo/
├── controller/
│   └── TodoController.java
├── service/
│   ├── TodoService.java
│   └── TodoServiceImpl.java
└── repository/
    ├── TodoRepository.java
    └── TodoRepositoryImpl.java
```

#### Key Concepts

* **Separation of concerns** for maintainability
* **Constructor-based dependency injection**
* **Repository pattern** as a foundation for database support

💡 **Outcome**: A clean and scalable backend ready for database integration and advanced features.

---

## 🎯 Progress Tracking

| Chapter | Status | Estimated Time | Priority |
|---------|--------|----------------|----------|
| 1. Quickstart | Foundation | 30 mins | 🔥🔥🔥 |
| 2. REST API | Core Feature | 2 hrs | 🔥🔥🔥 |
| 3. Architecture | Structure | 1 hr | 🔥🔥 |

---

### 4. 📊 Data Validation & Error Handling 🔥🔥 (2 hrs)

Improve your Todo API by adding input validation and clear error handling.

#### What You’ll Build

* Validate user input when creating or updating Todos
* Use custom exceptions for specific errors (like Todo not found)
* Handle errors globally with a centralized exception handler
* Return consistent and helpful error responses

#### Key Concepts

* Bean Validation with annotations (`@NotNull`, `@Size`, etc.)
* Using `@Valid` in controller methods to trigger validation
* Custom exception classes for business logic errors
* `@ControllerAdvice` for global error handling and clean API responses

#### Example Validation (in a DTO or entity)

```java
public class TodoRequest {
    @NotBlank(message = "Title is required")
    @Size(max = 100, message = "Title must be at most 100 characters")
    private String title;

    @Size(max = 500, message = "Description cannot exceed 500 characters")
    private String description;

    @NotNull(message = "Completion status is required")
    private Boolean completed;

    // getters and setters
}
```

#### Example Global Error Handler

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Object> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> {
            errors.put(error.getField(), error.getDefaultMessage());
        });
        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(TodoNotFoundException.class)
    public ResponseEntity<Object> handleTodoNotFound(TodoNotFoundException ex) {
        Map<String, String> error = Map.of("error", ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    // add other handlers as needed
}
```

💡 **Outcome**: Your API will reject invalid input with clear messages and handle errors consistently for a better developer experience.

---

### 5. 🛢️ Persistent Storage with Spring Data JPA 🔥🔥🔥 (3 hrs)

In this chapter, you’ll replace the in-memory store with a real database using **Spring Data JPA** — a powerful way to map Java objects to database tables.

#### 🧱 What You'll Build

* A `@Entity`-annotated `Todo` class mapped to a `todos` table
* A Spring Data `JpaRepository` interface for database access
* A `@ManyToOne` relationship between `Todo` and `User`
* Custom query methods using Spring conventions and `@Query`
* Automatic transaction management with Spring

---

#### 🧠 Key Concepts Explained

* **ORM (Object-Relational Mapping)**: Maps Java classes (like `Todo`) to DB tables.
* **JPA Annotations**: Like `@Entity`, `@Id`, `@Column`, used to define DB schema.
* **Spring Data JPA**: Lets you write interfaces, not boilerplate SQL.
* **Lazy Loading**: With `fetch = FetchType.LAZY`, related objects (like `User`) are only loaded when accessed.
* **Transactions**: `JpaRepository` methods are transactional by default. For custom logic, use `@Transactional` on service methods.

---

#### ✅ Sample JPA Entity

```java
@Entity
@Table(name = "todos")
public class Todo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotNull(message = "Title is required")
    @Size(min = 1, max = 100)
    @Column(nullable = false)
    private String title;

    @Size(max = 500)
    private String description;

    @Column(nullable = false)
    private boolean completed;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @Column(name = "created_at", nullable = false, updatable = false)
    @CreationTimestamp
    private LocalDateTime createdAt;

    // Getters and setters
}
```

---

#### 📦 Repository Interface

```java
@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
    List<Todo> findByUserIdOrderByCreatedAtDesc(Long userId);

    @Query("SELECT t FROM Todo t WHERE t.user.id = :userId AND t.completed = :completed")
    List<Todo> findByUserIdAndCompleted(@Param("userId") Long userId, @Param("completed") boolean completed);
}
```

---

#### ⚙️ Notes

* You don’t have to implement the repository — Spring handles it.
* `findByUserIdOrderByCreatedAtDesc` uses Spring’s naming conventions to generate a query.
* `@Query(...)` gives you full control for custom logic.
* If your main class is not in the root package, use `@EntityScan` and `@EnableJpaRepositories`.

---

#### 🧪 Testing Tip

Once connected to a real DB (e.g. H2, PostgreSQL, MySQL), you can test with:

```bash
curl -X POST http://localhost:8080/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Finish JPA chapter", "completed": false}'
```

---

#### 💡 Outcome

You now have a **production-ready Todo API** with persistent storage, query filtering, and relational mapping — using clean, idiomatic Spring Data JPA.

---

### 6. 🧪 Testing Fundamentals 🔥🔥 (2 hrs)

Ensure the reliability of your Todo application through comprehensive testing.

#### What You'll Build
- Unit tests for the service layer using JUnit 5 and Mockito
- Integration tests for the repository layer with @DataJpaTest
- API tests for the controller using MockMvc
- Testcontainers to run PostgreSQL during integration tests
- Code coverage reports with Jacoco

#### Key Concepts
- **JUnit 5 & Mockito:** Frameworks for unit testing and mocking dependencies
- **MockMvc:** For testing Spring MVC controllers without starting the server
- **@DataJpaTest:** To test JPA repositories with an in-memory or containerized database
- **Testcontainers:** Runs real PostgreSQL in Docker during tests for realistic integration
- **Jacoco:** Tracks and reports test coverage

#### Example: Unit Test for Service Layer
```java
@ExtendWith(MockitoExtension.class)
class TodoServiceTest {
    @Mock
    private TodoRepository todoRepository;

    @InjectMocks
    private TodoServiceImpl todoService;

    @Test
    void createTodo_ShouldReturnSavedTodo() {
        Todo todo = new Todo("Test Todo", "Description", false);
        when(todoRepository.save(any(Todo.class))).thenReturn(todo);

        Todo result = todoService.create(todo);

        assertNotNull(result);
        assertEquals("Test Todo", result.getTitle());
        verify(todoRepository).save(any(Todo.class));
    }
}
````

#### Example: Controller Test with MockMvc

```java
@WebMvcTest(TodoController.class)
class TodoControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private TodoService todoService;

    @Test
    void getAllTodos_ShouldReturnTodoList() throws Exception {
        List<Todo> todos = Arrays.asList(new Todo("Todo 1"), new Todo("Todo 2"));
        when(todoService.findAll()).thenReturn(todos);

        mockMvc.perform(get("/api/todos"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$", hasSize(2)))
               .andExpect(jsonPath("$[0].title", is("Todo 1")));
    }
}
```

#### Outcome

A well-tested Todo application with high confidence in your code’s correctness, reliable database integration, and clean API behavior.

Next: [Deployment and Dockerization ➡️](/docs/07-deployment)

---

### 7. 🔐 Security Essentials 🔥🔥🔥 (2.5 hrs)

Protect your Todo application with authentication and authorization.

#### What You'll Build
- User registration and login
- JWT (JSON Web Token) based authentication
- Role-based access control
- Secure Todo items per user
- Password encryption

#### Key Concepts
- Spring Security configuration
- JWT creation and validation
- UserDetails and UserDetailsService
- BCrypt password encoding
- Method-level security with @PreAuthorize

#### Security Configuration
```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .addFilterBefore(jwtAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
    }

    // Other configurations (password encoder, authentication manager, etc.)
}
```

#### Secured Controller Method
```java
@RestController
@RequestMapping("/api/todos")
public class TodoController {
    @GetMapping
    @PreAuthorize("hasRole('USER')")
    public List<Todo> getUserTodos(Authentication authentication) {
        UserDetails userDetails = (UserDetails) authentication.getPrincipal();
        return todoService.getTodosByUsername(userDetails.getUsername());
    }
}
```

💡 **Outcome**: A secure Todo application with user authentication and protected resources.

---

### 8. 🌐 Spring Data REST & Documentation 🔥🔥 (2 hrs)

Simplify API development and improve documentation for your Todo application.

#### What You'll Build
- Expose Todo API via Spring Data REST
- API documentation with OpenAPI 3 (Swagger)
- HAL explorer integration
- Custom endpoints alongside Spring Data REST

#### Key Concepts
- Spring Data REST repositories
- HAL (Hypertext Application Language)
- OpenAPI specifications
- Swagger UI

#### Spring Data REST Repository
```java
@RepositoryRestResource(collectionResourceRel = "todos", path = "todos")
public interface TodoRepository extends JpaRepository<Todo, Long> {
    List<Todo> findByUserUsername(@Param("username") String username);
    
    @Query("SELECT t FROM Todo t WHERE t.user.username = :username AND t.completed = :completed")
    List<Todo> findByUsernameAndCompleted(@Param("username") String username, @Param("completed") boolean completed);
}
```

#### OpenAPI Configuration
```java
@Configuration
@OpenAPIDefinition(info = @Info(title = "Todo API", version = "v1"))
public class OpenApiConfig {
    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
                .components(new Components()
                        .addSecuritySchemes("bearerAuth",
                                new SecurityScheme().type(SecurityScheme.Type.HTTP).scheme("bearer").bearerFormat("JWT")))
                .info(new Info().title("Todo API").version("v1"))
                .addSecurityItem(new SecurityRequirement().addList("bearerAuth"));
    }
}
```

💡 **Outcome**: A self-documenting Todo API with simplified CRUD operations and interactive documentation.

---

### 9. ⚡ Caching & Performance 🔥🔥 (2 hrs)

Optimize your Todo application for speed and efficiency.

#### What You'll Build
- Cache frequently accessed Todo data
- Redis integration for distributed caching
- Conditional caching strategies
- Cache eviction policies
- Performance monitoring

#### Key Concepts
- Spring Cache abstraction
- Redis as a cache store
- @Cacheable, @CachePut, and @CacheEvict annotations
- Cache configuration and customization
- Monitoring cache hit/miss rates

#### Caching Configuration
```java
@Configuration
@EnableCaching
public class CacheConfig {
    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        RedisCacheConfiguration cacheConfig = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(10))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
        
        return RedisCacheManager.builder(redisConnectionFactory)
                .cacheDefaults(cacheConfig)
                .build();
    }
}
```

#### Caching in Service Layer
```java
@Service
public class TodoServiceImpl implements TodoService {
    @Cacheable(value = "todos", key = "#userId")
    public List<Todo> getUserTodos(Long userId) {
        return todoRepository.findByUserId(userId);
    }

    @CachePut(value = "todos", key = "#result.id")
    public Todo createTodo(Todo todo) {
        return todoRepository.save(todo);
    }

    @CacheEvict(value = "todos", key = "#id")
    public void deleteTodo(Long id) {
        todoRepository.deleteById(id);
    }
}
```

💡 **Outcome**: A high-performance Todo application with optimized data access and reduced database load.

---

### 10. 🚀 Configuration & Deployment 🔥🔥 (2.5 hrs)

Prepare your Todo application for different environments and deployment scenarios.

#### What You'll Build
- Environment-specific configurations
- Dockerize the Todo application
- Multi-container setup with docker-compose
- Cloud deployment (AWS Elastic Beanstalk example)
- Basic CI/CD pipeline

#### Key Concepts
- Spring profiles
- Dockerfile and docker-compose
- Environment variables in Spring Boot
- Cloud platform basics (AWS, GCP, or Azure)
- CI/CD concepts and tools (e.g., GitHub Actions)

#### Application Properties for Multiple Environments
```yaml
# application.yml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}

---
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    url: jdbc:h2:mem:tododb
    driver-class-name: org.h2.Driver
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect

---
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
  jpa:
    database-platform: org.hibernate.dialect.PostgreSQLDialect
```

#### Dockerfile
```dockerfile
FROM openjdk:11-jre-slim
VOLUME /tmp
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

#### Docker Compose
```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - DB_HOST=db
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=tododb
      - POSTGRES_PASSWORD=secret
```

💡 **Outcome**: A Todo application ready for deployment across various environments, from local development to production cloud services.

---

### 11. 📊 Monitoring & Observability 🌿 (2 hrs)

Gain insights into your Todo application's performance and health.

#### What You'll Build
- Spring Boot Actuator integration
- Custom metrics for Todo operations
- Prometheus & Grafana setup
- Comprehensive logging strategy
- Distributed tracing with Spring Cloud Sleuth

#### Key Concepts
- Actuator endpoints
- Micrometer metrics
- Log aggregation
- Distributed tracing
- Monitoring dashboards

#### Actuator Configuration
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,prometheus
  endpoint:
    health:
      show-details: always
  metrics:
    export:
      prometheus:
        enabled: true
```

#### Custom Metric
```java
@Service
public class TodoServiceImpl implements TodoService {
    private final MeterRegistry meterRegistry;

    public TodoServiceImpl(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
    }

    public Todo createTodo(Todo todo) {
        Todo savedTodo = todoRepository.save(todo);
        meterRegistry.counter("todos.created").increment();
        return savedTodo;
    }
}
```

💡 **Outcome**: A Todo application with comprehensive monitoring and observability features.

---

### 12. 🚀 Advanced Features 🌿 (3 hrs)

Enhance your Todo application with advanced Spring capabilities.

#### What You'll Build
- Scheduled Todo reminders
- Asynchronous operations for long-running tasks
- WebSocket for real-time updates
- Event-driven architecture using Spring Events

#### Key Concepts
- @Scheduled annotation
- @Async for asynchronous methods
- WebSocket communication
- Application events and listeners

#### Scheduled Reminder
```java
@Service
public class ReminderService {
    @Scheduled(cron = "0 0 8 * * ?")  // Every day at 8 AM
    public void sendDailyReminders() {
        List<Todo> dueTodos = todoRepository.findDueTodos();
        // Logic to send reminders
    }
}
```

#### WebSocket Configuration
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
    @Override
    public void configureMessageBroker(MessageBrokerRegistry config) {
        config.enableSimpleBroker("/topic");
        config.setApplicationDestinationPrefixes("/app");
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws").withSockJS();
    }
}
```

💡 **Outcome**: A feature-rich Todo application leveraging advanced Spring capabilities.

---

### 13. 📈 API Evolution & Versioning 🌿 (2 hrs)

Prepare your Todo API for long-term maintenance and evolution.

#### What You'll Build
- API versioning strategies
- Backward compatibility measures
- API documentation versioning
- Database migration strategies

#### Key Concepts
- URI versioning
- Header-based versioning
- Content negotiation
- Database schema evolution
- API deprecation process

#### Versioned Controller
```java
@RestController
@RequestMapping("/api/v1/todos")
public class TodoControllerV1 {
    // V1 endpoints
}

@RestController
@RequestMapping("/api/v2/todos")
public class TodoControllerV2 {
    // V2 endpoints with new features
}
```

#### Content Negotiation
```java
@GetMapping(produces = {"application/vnd.myapp.todo.v1+json", "application/vnd.myapp.todo.v2+json"})
public ResponseEntity<Todo> getTodo(@RequestHeader("Accept") String acceptHeader) {
    if (acceptHeader.contains("v2")) {
        return ResponseEntity.ok(new TodoV2());
    }
    return ResponseEntity.ok(new TodoV1());
}
```

💡 **Outcome**: A Todo API prepared for future changes and long-term maintenance.

---

### 14. 🌐 Microservices Foundation 🌿 (4 hrs)

Transform your monolithic Todo application into a microservices architecture.

#### What You'll Build
- Split Todo into microservices (e.g., User Service, Todo Service)
- Implement service discovery with Eureka
- Set up an API Gateway
- Centralize configuration with Config Server
- Implement circuit breakers for resilience

#### Key Concepts
- Microservices architecture principles
- Service registration and discovery
- API Gateway patterns
- Centralized configuration management
- Circuit breaker pattern

#### Eureka Service
```java
@SpringBootApplication
@EnableEurekaServer
public class ServiceRegistryApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceRegistryApplication.class, args);
    }
}
```

#### API Gateway Configuration
```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: todo-service
          uri: lb://todo-service
          predicates:
            - Path=/api/todos/**
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/users/**
```

💡 **Outcome**: A foundation for scaling your Todo application into a microservices architecture.

---
Certainly! Here's the corrected chapter index with proper numbering:

---

## Todo App Evolution Through Chapters:

1. **Chapter 1**: First endpoint returning "Hello, Todo!"
2. **Chapter 2**: REST API with full CRUD operations
3. **Chapter 3**: Layered Architecture with Controller, Service, Repository
4. **Chapter 4**: Validated Todo items with proper error handling
5. **Chapter 5**: Persistent Todos with user relationships in a database
6. **Chapter 6**: Comprehensively tested Todo features
7. **Chapter 7**: Secure Todo access with user authentication
8. **Chapter 8**: RESTful Todo API with auto-generated documentation
9. **Chapter 9**: High-performance Todo operations with caching
10. **Chapter 10**: Deployable Todo service across various environments
11. **Chapter 11**: Observable Todo application with monitoring
12. **Chapter 12**: Feature-rich Todo app with scheduled reminders and real-time updates
13. **Chapter 13**: Evolvable Todo API ready for long-term maintenance
14. **Chapter 14**: Scalable Todo platform with microservices architecture

## Priority Guide:
- 🔥🔥🔥 Must learn (Chapters 1-7)
- 🔥🔥 Should learn (Chapters 8-10)
- 🌿 Nice to have (Chapters 11-14)

This comprehensive learning path takes you from a basic Todo application to an enterprise-grade, scalable system, covering essential Spring Boot concepts and advanced software engineering practices.

---

## 💡 Why This Tutorial?

- No boring theoretical lectures
- Learn by building real apps
- Clear, no-BS explanations
- Bad jokes included at no extra charge

Let’s start building something awesome! 🚀

---
### 💬 Comments & Discussion

<script src="https://giscus.app/client.js"
        data-repo="minweny/spring-zero-to-hero"
        data-repo-id="R_kgDOO2Ofug"
        data-category="General"
        data-category-id="DIC_kwDOO2Ofus4CrDkc"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="light"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
<div class="giscus"></div>
