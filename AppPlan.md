# Driving License Test Practice App - Project Plan (.NET 10 / C#)

## 1) Product Goal
Build a cross-platform study app that helps users prepare for a driving knowledge exam by taking timed or untimed multiple-choice practice tests, reviewing results, and tracking progress over time.

## 2) Scope
### Core (MVP)
- Start a practice test from the home screen.
- Ask 20-30 random multiple-choice questions per attempt.
- Navigate between questions (next, previous, jump to index).
- Persist answers during a test session.
- Submit test and display score + pass/fail summary.
- Use mock/local question source first (JSON/in-memory).

### Phase 2
- Replace mock source with SQLite.
- Add history of past attempts.
- Add category breakdown (traffic signs, rules, penalties, safety).

### Optional Phase 3
- Add login/identity for personalized history.
- Add SQL Server provider for production hosting.
- Add admin import flow for question bank updates.

## 3) Recommended .NET 10 Architecture
Use **ASP.NET Core Razor Pages (or Blazor Server)** for fast delivery and low overhead.

Suggested layered structure:
- `DriverKnowledgeTest.Web` (UI + endpoints/pages)
- `DriverKnowledgeTest.Application` (use cases/services)
- `DriverKnowledgeTest.Domain` (entities/value objects/rules)
- `DriverKnowledgeTest.Infrastructure` (data access: mock, SQLite, SQL Server)

Key design principles:
- Depend on abstractions in Application/Domain.
- Keep UI thin: orchestration only.
- Use repository interfaces so mock and DB providers are swappable.

## 4) Screens and UX Flow
1. **Login (Optional)**
   - Continue as guest OR login.
2. **Home**
   - Start Test button.
   - Optional links: Past Attempts, Settings.
3. **Question View**
   - Question text + 4 options.
   - Select answer, Next/Previous controls.
   - Question index navigator (e.g., 1..20).
   - Submit button on final step (or always available with confirmation).
4. **Results Summary**
   - Score (correct/total).
   - Percentage + pass/fail.
   - Review incorrect answers.
   - Retry button.
5. **Past Attempts (Optional)**
   - Attempt date, score, percentage, duration.
   - Optional detailed review per attempt.

## 5) Domain Model (Initial)
### Entities
- `Question`
  - `Id`, `Text`, `Category`, `Difficulty`, `IsActive`
- `AnswerOption`
  - `Id`, `QuestionId`, `Text`, `IsCorrect`
- `TestAttempt`
  - `Id`, `UserId?`, `StartedAt`, `CompletedAt`, `Score`, `TotalQuestions`
- `AttemptAnswer`
  - `AttemptId`, `QuestionId`, `SelectedOptionId`, `IsCorrect`
- `User` (optional)
  - `Id`, `Email/Username`, `PasswordHash` (if local auth)

### Rules
- A generated test has between 20 and 30 unique questions.
- Each question has exactly one correct option.
- Score is calculated at submit time from selected options.

## 6) Application Services
- `ITestSessionService`
  - CreateSession(questionCount)
  - SaveAnswer(sessionId, questionId, optionId)
  - GetSessionProgress(sessionId)
  - Submit(sessionId)
- `IQuestionProvider`
  - GetRandomQuestions(count)
- `IAttemptHistoryService`
  - ListAttempts(userId or guest token)
  - GetAttemptDetails(attemptId)

## 7) Data Strategy
### MVP: Mock Data
- Store a starter question bank in `questions.json`.
- Load into memory on app startup.

### DB Phase: SQLite
- EF Core provider: `Microsoft.EntityFrameworkCore.Sqlite`.
- Migration-driven schema.
- Keep repository contracts stable to avoid UI changes.

### SQL Server Phase (Optional)
- Add second EF Core provider via config switch:
  - `UseSqlite(...)` for local dev
  - `UseSqlServer(...)` for hosted environment

## 8) Suggested Milestones
### Milestone 1 - Skeleton
- Create solution + projects.
- Wire DI and base layout.
- Add mock question provider.

### Milestone 2 - Knowledge Test Flow
- Implement home -> question -> results.
- Add navigation and answer persistence.
- Add scoring and summary page.

### Milestone 3 - Persistence
- Add SQLite + EF Core models.
- Store attempts and answers.
- Add Past Attempts screen.

### Milestone 4 - Optional Identity
- Add ASP.NET Core Identity or minimal auth.
- Associate attempts with users.

### Milestone 5 - Hardening
- Validation, error handling, logging.
- Add unit/integration tests.
- Seed/expand question bank.

## 9) Testing Plan
- **Unit tests**
  - Score calculation logic.
  - Random question selection (count/uniqueness).
- **Integration tests**
  - Full test submission flow.
  - Persistence of attempts and retrieval.
- **UI/smoke tests**
  - Start test, navigate, submit, view results.

## 10) Non-Functional Requirements
- Fast startup for local training use.
- Maintainable architecture for easy DB swap.
- Secure handling of credentials (if login added).
- Accessibility basics: keyboard navigation, semantic labels.

## 11) Initial Backlog (Implementation-Ready)
1. Create solution and 4-project structure.
2. Add question JSON seed and loader.
3. Build Home + Question + Results pages.
4. Implement session state (in-memory for MVP).
5. Implement scoring logic and result DTO.
6. Add SQLite persistence for attempts.
7. Build Past Attempts page.
8. Add authentication (optional).

## 12) Recommended NuGet Packages (Early)
- `Microsoft.EntityFrameworkCore`
- `Microsoft.EntityFrameworkCore.Sqlite`
- `Microsoft.EntityFrameworkCore.Design`
- `FluentValidation` (optional)
- `Serilog.AspNetCore` (optional)
- `xunit` + `Microsoft.NET.Test.Sdk` + `FluentAssertions`

## 13) Definition of Done for MVP
- User can complete a 20-30 question test.
- User can move backward/forward without losing answers.
- App shows final score and incorrect answers.
- At least basic tests pass for scoring and question generation.
- Project runs locally with documented setup.
