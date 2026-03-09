# Architecture Overview

Driving License Test Practice App (.NET 10 / ASP.NET Core MVC)

---

# 1. System Overview

The Driving License Test Practice App is a **web-based study portal** built using **ASP.NET Core MVC**.
It allows users to practice driving knowledge tests through randomized multiple-choice quizzes.

The application follows a **layered MVC architecture** with clear separation between:

* Presentation (MVC)
* Application services
* Data access repositories
* Data storage

The design supports evolving from **mock data → SQLite → SQL Server** without changing UI logic.

---

# 2. High-Level Architecture

```
Browser (User)
      ↓
ASP.NET Core MVC
      ↓
Controllers
      ↓
Application Services
      ↓
Repositories
      ↓
Data Source
(JSON → SQLite → SQL Server)
```

Responsibilities:

| Layer           | Responsibility                                 |
| --------------- | ---------------------------------------------- |
| MVC Controllers | Handle HTTP requests and orchestrate workflows |
| Services        | Implement application logic                    |
| Repositories    | Abstract data access                           |
| Data Sources    | JSON seed file or database                     |

---

# 3. MVC Flow (Request Lifecycle)

Example: Starting a quiz.

```
User clicks "Start Test"
        ↓
HomeController.Start()
        ↓
TestController.Start()
        ↓
IQuestionRepository.GetRandomQuestions()
        ↓
TestSessionService.CreateSession()
        ↓
Redirect → Question View
```

Navigation through questions updates session state until submission.

---

# 4. Application Components

## Controllers

Controllers handle routing and coordinate services.

| Controller        | Responsibility                |
| ----------------- | ----------------------------- |
| HomeController    | Home screen and start test    |
| TestController    | Quiz flow and scoring         |
| AccountController | Optional login / guest access |

Controllers remain **thin**, delegating logic to services.

---

## Services

Services encapsulate core business logic.

### TestSessionService

Maintains the current test state in **session storage**.

Tracks:

* AttemptId
* Question order
* Selected answers
* Current question index

### ScoringService

Calculates quiz results.

```
Score = Number of Correct Answers
Percentage = Score / Total Questions
```

### CurrentUserService

Manages current user session.

Supports:

* Display-name login
* Guest mode
* Logout

---

## Repository Layer

The repository pattern allows the application to change storage providers easily.

Interface:

```
IQuestionRepository
```

Implementations:

| Repository                    | Data Source         |
| ----------------------------- | ------------------- |
| JsonQuestionRepository        | JSON seed file      |
| EfQuestionRepository (future) | SQLite / SQL Server |

---

# 5. Data Storage Strategy

## MVP

Questions stored in:

```
wwwroot/data/questions.seed.json
```

Advantages:

* No database required
* Fast startup
* Easy to modify question bank

---

## Phase 2

Switch to **SQLite via EF Core**

Benefits:

* Persist test attempts
* Support Past Attempts page
* Scalable architecture

---

## Phase 3

Add **SQL Server provider** for hosted deployment.

The repository interface ensures **no UI changes are required** when switching providers.

---

# 6. Session Management

The quiz state is stored in **ASP.NET Core session**.

```
TestSession
 ├─ AttemptId
 ├─ StartedAt
 ├─ QuestionIds[]
 ├─ Answers[QuestionId → SelectedChoice]
 └─ CurrentIndex
```

Advantages:

* Supports navigation between questions
* Prevents loss of answers
* Lightweight for MVP

---

# 7. Authentication Model

The application supports **optional login**.

Users may:

* Enter a **display name**
* Continue as **Guest**

User identity is stored in session.

Future upgrade path:

```
DisplayName Login
        ↓
ASP.NET Identity
        ↓
OAuth / External Providers
```

---

# 8. Domain Model

Core entities:

### Question

Represents a knowledge test question.

```
Question
 ├─ Id
 ├─ Text
 ├─ ChoiceA
 ├─ ChoiceB
 ├─ ChoiceC
 ├─ ChoiceD
 ├─ CorrectChoiceIndex
 └─ Explanation
```

### TestAttempt (future persistence)

```
TestAttempt
 ├─ Id
 ├─ UserId
 ├─ StartedAt
 ├─ CompletedAt
 ├─ Score
 └─ Percentage
```

### AttemptAnswer

```
AttemptAnswer
 ├─ AttemptId
 ├─ QuestionId
 ├─ SelectedChoiceIndex
 └─ IsCorrect
```

---

# 9. Scalability Considerations

The architecture supports growth through:

* Repository abstraction
* Service-based logic
* MVC separation

Potential improvements:

* Caching question banks
* Admin question management
* Category filtering
* Timed quizzes

---

# 10. Future Enhancements

Planned improvements include:

* Past Attempts dashboard
* Question categories
* Timed exam mode
* Question difficulty levels
* Admin panel for managing questions
* Cloud deployment support

---

# 11. Development Philosophy

The project follows these principles:

* **Thin Controllers**
* **Service-based business logic**
* **Repository abstraction**
* **Incremental evolution**
* **Minimal dependencies for MVP**

This ensures the system remains easy to understand, extend, and maintain.
