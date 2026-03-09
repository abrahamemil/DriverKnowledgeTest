# Driving License Test Practice App - Project Plan (.NET 10 / C#)

## 1) Product Goal
Build a lightweight **web portal** that helps users prepare for a driving knowledge exam by taking randomized multiple-choice practice tests, reviewing results, and optionally tracking attempts.

The system is implemented using **ASP.NET Core MVC** with session-based test state.

---

# 2) Scope

## Core
- Start a practice test from the home screen.
- Ask **20–30 random multiple-choice questions** per attempt.
- Navigate between questions (next / previous).
- Persist answers during the test session using **server session state**.
- Submit test and display:
  - Score
  - Percentage
  - Incorrect answers with correct solution.
- Use **JSON-based question source** initially.

## Phase 2
- Replace JSON question source with **SQLite using EF Core**.
- Persist test attempts and answers.
- Add **Past Attempts page** for logged-in users.

## Optional Phase 3
- Expand login system using **ASP.NET Core Identity**.
- Add **SQL Server provider** for hosted deployment.
- Add **admin interface** for managing question banks.

---

# 3) Application Architecture

The application follows a **classic ASP.NET Core MVC architecture** with supporting services.
```
Browser
↓
MVC Controllers
↓
Application Services
↓
Repositories
↓
Data Source (JSON → SQLite → SQL Server)
```
---

## Key Components

### Controllers
Handle HTTP requests and coordinate application flow.

- `HomeController` – Home page and test start
- `TestController` – Quiz flow (questions, answers, results)
- `AccountController` – Optional login / guest access

### Services
Encapsulate business logic.

- `TestSessionService`
- `ScoringService`
- `CurrentUserService`

### Repositories
Abstract question storage.

- `IQuestionRepository`
- `JsonQuestionRepository`
- (Future) `EfQuestionRepository`

### Views
Razor pages responsible for rendering UI.

---

# 4) Project Structure
```
DriverQuiz.Web
│
├── Controllers
│ ├── HomeController
│ ├── TestController
│ └── AccountController
│
├── Models
│ ├── Question
│ ├── TestAttempt
│ └── AttemptAnswer
│
├── Services
│ ├── IQuestionRepository
│ ├── JsonQuestionRepository
│ ├── TestSessionService
│ ├── ScoringService
│ └── CurrentUserService
│
├── ViewModels
│ ├── StartTestViewModel
│ ├── QuestionViewModel
│ ├── ResultsViewModel
│ └── LoginViewModel
│
├── Views
│ ├── Home
│ ├── Test
│ ├── Account
│ └── Shared
│
└── wwwroot
└── data
└── questions.seed.json
```
---

# 5) Screens and UX Flow

### 1. Login (Optional)
Users may:
- Enter a display name
- Continue as **Guest**

Session stores current user identity.

### 2. Home
Options:
- Start Test
- Logout

### 3. Question View
Displays:
- Question text
- Four answer options
- Navigation controls (Next / Previous)

Answers are stored in session.

### 4. Results Summary
Displays:
- Total score
- Percentage
- Incorrect questions with correct answers
- Option to start a new test

### 5. Past Attempts (Future)
Displays:
- Attempt date
- Score
- Percentage
- Detailed review

---

# 6) Domain Model (Current)

## Entities

### Question
- `Id`
- `Text`
- `ChoiceA`
- `ChoiceB`
- `ChoiceC`
- `ChoiceD`
- `CorrectChoiceIndex`
- `Explanation`
- `Category`
- `IsActive`

### TestAttempt (future persistence)
- `Id`
- `UserId`
- `UserDisplayName`
- `StartedAt`
- `CompletedAt`
- `Score`
- `Percentage`

### AttemptAnswer (future persistence)
- `AttemptId`
- `QuestionId`
- `SelectedChoiceIndex`
- `IsCorrect`

### AppUser
- `Id`
- `DisplayName`
- `IsGuest`

---

# 7) Data Strategy

## MVP
Questions stored in:
`wwwroot/data/questions.seed.json`

Loaded through:
`JsonQuestionRepository`

## Future Database Layer

EF Core will be used to support:

### SQLite
For local persistence.

### SQL Server
For hosted deployment.

Repository interface allows switching providers without UI changes.

---

# 8) Session Management

The app uses **ASP.NET Core Session State** to maintain the current quiz.

Session contains:
```
TestSession
├─ AttemptId
├─ StartedAt
├─ QuestionIds[]
├─ Answers[QuestionId → SelectedOption]
└─ CurrentIndex
```
This allows:
- navigation between questions
- saving answers before submission

---

# 9) Suggested Milestones

## Milestone 1 – MVC Skeleton
- Create MVC solution
- Add layout and routing
- Implement question JSON loader

## Milestone 2 – Quiz Flow
- Implement Start Test
- Question navigation
- Answer persistence
- Results page

## Milestone 3 – Persistence
- Add SQLite with EF Core
- Store attempts and answers
- Build Past Attempts screen

## Milestone 4 – Authentication
- Replace display-name login with Identity
- Link attempts to user accounts

## Milestone 5 – Hardening
- Logging
- Validation
- Expand question bank
- Improve UI

---

# 10) Testing Plan

## Unit Tests
- Score calculation
- Random question selection

## Integration Tests
- Full quiz workflow
- Session handling
- Results generation

## Manual UI Testing
- Start test
- Navigate questions
- Submit test
- Verify scoring

---

# 11) Non-Functional Requirements

- Fast startup
- Minimal dependencies
- Easily replaceable data providers
- Maintainable MVC structure
- Accessible UI (keyboard navigation, semantic labels)

---

# 12) Recommended NuGet Packages

Core:
```
Microsoft.EntityFrameworkCore
Microsoft.EntityFrameworkCore.Sqlite
Microsoft.EntityFrameworkCore.Design
```

Optional:
```
FluentValidation
Serilog.AspNetCore
```

Testing:
```
xunit
Microsoft.NET.Test.Sdk
FluentAssertions
```

# 13) Definition of Done (MVP)

The MVP is complete when:
- User can login or continue as guest
- User can take a **20–30 question test**
- User can move forward/backward without losing answers
- App displays **score and incorrect answers**
- Project runs locally with documented setup
