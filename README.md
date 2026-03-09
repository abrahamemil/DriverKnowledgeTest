
# Driving License Test Practice App (ASP.NET Core MVC)

A lightweight **ASP.NET Core MVC web application** for practicing driving knowledge test questions.
The app allows users to take randomized multiple-choice tests, navigate between questions, and view results at the end of the test.

This project is designed as a **learning project** using modern .NET features (C# 14 / .NET 10 preview) and a clean MVC structure.

---

# Features

### Core

* Randomly select **20–30 multiple-choice questions**
* Navigate **Next / Previous** between questions
* Save selected answers during the test
* Display **final score and percentage**
* Review **incorrect answers with correct solution**

### User Access

* Optional login
* **Guest mode** supported
* Session-based quiz state

### Data

* Questions loaded from **JSON seed file**
* Repository pattern for easy switch to:

  * SQLite
  * SQL Server

---

# Tech Stack

* **.NET 10 (Preview)**
* **C# 14**
* **ASP.NET Core MVC**
* Razor Views
* Session state
* Dependency Injection
* JSON data source (initial)

Planned:

* EF Core + SQLite
* User attempt history

---

# Project Structure

```
DriverQuiz.Web
│
├── Controllers
│   ├── HomeController
│   ├── TestController
│   └── AccountController
│
├── Models
│   ├── Question
│   ├── TestAttempt
│   └── AttemptAnswer
│
├── Services
│   ├── IQuestionRepository
│   ├── JsonQuestionRepository
│   ├── TestSessionService
│   ├── ScoringService
│   └── CurrentUserService
│
├── ViewModels
│   ├── StartTestViewModel
│   ├── QuestionViewModel
│   ├── ResultsViewModel
│   └── LoginViewModel
│
├── Views
│   ├── Home
│   ├── Test
│   ├── Account
│   └── Shared
│
└── wwwroot
    └── data
        └── questions.seed.json
```

---

# Application Flow

```
Login / Guest
      ↓
Home
      ↓
Start Test
      ↓
Question View
   (Next / Previous)
      ↓
Finish Test
      ↓
Results Summary
```

---

# Running the Application

### Requirements

* .NET 10 SDK (Preview)

### Run

```bash
dotnet restore
dotnet run
```

Then open the URL shown in the terminal (typically `https://localhost:5001`).

---

# Learning Goals

This project demonstrates:

* MVC architecture in ASP.NET Core
* Session-based workflow
* Repository pattern
* ViewModel driven UI
* Dependency injection
* Incremental architecture evolution (Mock → Database)

---

# License

Educational / personal project.
