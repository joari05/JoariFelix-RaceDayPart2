# RaceDay Part 2: RESTful API Development

This project implements the RaceDay API using ASP.NET Core Web API, C#, SQL Server and Entity Framework Core. It follows the entities and endpoint plan from Part 1.

## Requirements
- Visual Studio 2022 with the ASP.NET and web development workload
- .NET 8 SDK
- SQL Server or SQL Server LocalDB
- SQL Server Management Studio (optional)

## Database configuration
The default connection string is in `RaceDay.Api/appsettings.json` and uses LocalDB. Change `ConnectionStrings:RaceDay` to match your SQL Server instance. For example, SQL Server Express may use `Server=.\SQLEXPRESS;Database=RaceDayDB;Trusted_Connection=True;TrustServerCertificate=True`.

Create the EF Core database from the project directory:
```bash
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate --project RaceDay.Api
dotnet ef database update --project RaceDay.Api
```
The database entities and relationships match the Part 1 schema. If you prefer the supplied SQL script as the source of truth, ensure its database/table names match before using migrations; do not run both approaches against the same existing database without checking first.

## Run the API
```bash
dotnet restore RaceDay.sln
dotnet build RaceDay.sln
dotnet run --project RaceDay.Api
```
Open the HTTPS URL shown in the terminal followed by `/swagger`. Swagger is enabled in Development. For Visual Studio, set `RaceDay.Api` as the startup project and press F5.

## Roles and session authentication
- **Organiser:** creates and updates own events, manages routes and categories, views enrolments for own events, and records/updates/deletes results.
- **Participant:** enrols in events, views/cancels own enrolments, and views own results.
- Both roles can view and update their own profile.

Register at `POST /api/auth/register`, then log in at `POST /api/auth/login`. The API stores `UserID` and `Role` in a server-side ASP.NET session and sends a session cookie. Keep the same cookie for subsequent requests. Use `POST /api/auth/logout` to clear the session. Passwords are stored as hashes, never as plain text. For a real deployment, use a persistent distributed session store and HTTPS only.

## Endpoint overview
- Authentication: `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/logout`
- Profile: `GET /api/users/me`, `PUT /api/users/me`
- Events: `GET /api/events`, `GET /api/events/{eventId}`, `POST /api/events`, `PUT /api/events/{eventId}`, `DELETE /api/events/{eventId}`
- Routes: `GET /api/events/{eventId}/route`, `POST /api/events/{eventId}/route`, `PUT /api/events/{eventId}/route`
- Categories: `GET /api/categories`, `GET /api/events/{eventId}/categories`, `POST /api/events/{eventId}/categories`, `PUT /api/categories/{categoryId}`, `DELETE /api/categories/{categoryId}`
- Enrolments: `POST /api/events/{eventId}/enrolments`, `GET /api/enrolments/me`, `GET /api/events/{eventId}/enrolments`, `DELETE /api/enrolments/{enrolmentId}`
- Results: `POST /api/events/{eventId}/results`, `GET /api/results/me`, `GET /api/events/{eventId}/results`, `PUT /api/results/{resultId}`, `DELETE /api/results/{resultId}`

## Tests
Run locally with:
```bash
dotnet test RaceDay.sln --configuration Release
```
The test project checks public event access and rejects unauthenticated profile/enrolment requests. Add further integration tests for register/login, organiser-versus-participant permissions, enrolment persistence and validation before final submission.

## GitHub Actions
The workflow at `.github/workflows/part2.yml` restores, builds and runs tests on pushes and pull requests to `main` and `part2`. Push the code to your repository, open the Actions tab and capture a screenshot of a successful green run. Add that screenshot here before submission.

## Submission checklist
- [ ] Confirm the database connection and run migrations
- [ ] Test all endpoints in Swagger with organiser and participant sessions
- [ ] Expand the test suite to cover every rubric scenario
- [ ] Push at least 20 genuine, meaningful commits to your own GitHub repository
- [ ] Add a real green-build screenshot
- [ ] Record and upload an unlisted YouTube walkthrough, then add its URL here
- [ ] Submit the repository link on ARC

Do not claim the build, tests, screenshot, commit count or video are complete until you have performed those actions in your own environment.
