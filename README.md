# Job Application Tracker

A full-stack web application for managing and analyzing job applications throughout the recruitment process.

The system provides a centralized workspace to track companies, positions, application statuses, interviews, notes, and application history. It also provides analytics and automated notifications to help users stay organized during an active job search.

## Features

### Authentication & Authorization

* JWT-based authentication
* User registration and login
* Secure password hashing
* Access and refresh token management
* User-specific application data
* Protected API endpoints

### Job Application Management

* Create, update, and delete job applications
* Track company and position information
* Record salary ranges
* Track application dates
* Manage application status
* Add notes and additional information
* View complete application history

### Application Status Tracking

Applications can move through different recruitment stages:

```text
Applied
   ↓
HR Screening
   ↓
Technical Interview
   ↓
Final Interview
   ↓
Offer
```

Possible statuses include:

* Applied
* Screening
* Interview
* Final Interview
* Offer
* Rejected
* Withdrawn

### Interview Management

* Schedule interviews
* Record interview type and location
* Store interview notes
* Track upcoming interviews
* Receive reminders for scheduled interviews

### Dashboard & Analytics

The dashboard provides an overview of the user's job search:

* Total applications
* Applications by status
* Interview rate
* Offer rate
* Applications over time
* Recent applications
* Upcoming interviews

Example:

```text
Applications       42
Interviews         13
Offers              3
Rejected           17
Waiting             9

Interview Rate     31%
Offer Rate          7%
```

### Search & Filtering

Applications can be searched and filtered by:

* Company
* Position
* Application status
* Salary range
* Application date

The backend also supports pagination and sorting for larger datasets.

### Audit History

Important application changes are recorded in an audit history.

Example:

```text
Aug 18, 10:31
Status changed
WAITING → INTERVIEW

Aug 18, 10:30
Interview scheduled

Aug 15, 14:20
Application created
```

### Automated Notifications

A background process checks upcoming interviews and creates notifications for important events.

The notification system is designed around asynchronous/background processing so that scheduled tasks do not block normal API requests.

---

# Architecture

```text
                    ┌─────────────────────┐
                    │      Vue 3          │
                    │    TypeScript       │
                    └──────────┬──────────┘
                               │
                              HTTP
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Spring Boot      │
                    │       REST API      │
                    └──────────┬──────────┘
                               │
                 ┌─────────────┼─────────────┐
                 │             │             │
                 ▼             ▼             ▼
          ┌────────────┐ ┌───────────┐ ┌─────────────┐
          │ PostgreSQL │ │   Redis   │ │ Notification│
          │            │ │           │ │   Worker    │
          └────────────┘ └───────────┘ └─────────────┘
```

## Technology Stack

### Backend

* Java
* Spring Boot
* Spring Web
* Spring Security
* Spring Data JPA
* Bean Validation
* JWT
* Flyway
* OpenAPI / Swagger

### Database & Infrastructure

* PostgreSQL
* Redis
* Docker
* Docker Compose

### Frontend

* Vue 3
* TypeScript
* Vite

### Testing

* JUnit
* Mockito
* Spring Boot Test
* Testcontainers

---

# Database Design

The main entities are:

```text
User
 │
 ├── JobApplication
 │      │
 │      ├── ApplicationStatusHistory
 │      ├── Interview
 │      └── ApplicationNote
 │
 └── Company
```

### Main Tables

#### users

Stores authentication and user information.

```text
id
email
password_hash
created_at
updated_at
```

#### companies

Stores company information.

```text
id
name
website
industry
created_at
updated_at
```

#### job_applications

Stores individual job applications.

```text
id
user_id
company_id
position
salary_min
salary_max
status
applied_at
created_at
updated_at
```

#### interviews

Stores scheduled interviews.

```text
id
application_id
interview_type
scheduled_at
location
notes
created_at
updated_at
```

#### application_status_history

Stores changes made to an application's status.

```text
id
application_id
old_status
new_status
changed_at
```

---

# API

The backend exposes a RESTful API.

## Authentication

```http
POST /api/auth/register
POST /api/auth/login
POST /api/auth/refresh
POST /api/auth/logout
```

## Applications

```http
GET    /api/applications
GET    /api/applications/{id}
POST   /api/applications
PUT    /api/applications/{id}
DELETE /api/applications/{id}
```

## Interviews

```http
GET    /api/interviews
GET    /api/interviews/{id}
POST   /api/interviews
PUT    /api/interviews/{id}
DELETE /api/interviews/{id}
```

## Dashboard

```http
GET /api/dashboard/statistics
GET /api/dashboard/application-trends
GET /api/dashboard/upcoming-interviews
```

Interactive API documentation is available through OpenAPI/Swagger when running the backend locally.

---

# Example API Request

Create a new application:

```json
{
  "companyId": 1,
  "position": "Java Backend Engineer",
  "salaryMin": 50000,
  "salaryMax": 65000,
  "status": "APPLIED",
  "appliedAt": "2026-08-18"
}
```

Example response:

```json
{
  "id": 42,
  "company": "Example Technology",
  "position": "Java Backend Engineer",
  "salaryMin": 50000,
  "salaryMax": 65000,
  "status": "APPLIED",
  "appliedAt": "2026-08-18"
}
```

---

# Getting Started

## Prerequisites

Make sure the following are installed:

* Java 21+
* Node.js 20+
* Docker
* Docker Compose
* Git

## Clone the Repository

```bash
git clone https://github.com/<your-username>/job-application-tracker.git

cd job-application-tracker
```

## Start Infrastructure

```bash
docker compose up -d postgres redis
```

## Run the Backend

```bash
cd backend

./mvnw spring-boot:run
```

On Windows:

```powershell
mvnw.cmd spring-boot:run
```

The backend will start on:

```text
http://localhost:8080
```

## Run the Frontend

```bash
cd frontend

npm install
npm run dev
```

The frontend will be available through the Vite development server.

---

# Configuration

The application uses environment variables for configuration.

Example:

```env
SERVER_PORT=8080

DATABASE_URL=jdbc:postgresql://localhost:5432/jobtracker
DATABASE_USERNAME=jobtracker
DATABASE_PASSWORD=change-me

REDIS_HOST=localhost
REDIS_PORT=6379

JWT_SECRET=change-me
JWT_ACCESS_EXPIRATION=900
JWT_REFRESH_EXPIRATION=604800
```

Secrets should not be committed to the repository.

For local development, use a `.env` file or your preferred environment configuration mechanism.

---

# Testing

Run backend unit and integration tests:

```bash
./mvnw test
```

The project uses Testcontainers for integration tests that require a real PostgreSQL environment.

Example test architecture:

```text
JUnit
  │
  ▼
Spring Boot Test
  │
  ▼
Testcontainers
  │
  ▼
PostgreSQL
```

This allows database-related tests to run against an isolated environment rather than relying on a developer's local database.

---

# Development Workflow

The project follows a feature-based development workflow.

Typical workflow:

```text
Requirement
    ↓
Implementation
    ↓
Unit Tests
    ↓
Integration Tests
    ↓
Code Review
    ↓
Documentation
```

Database schema changes are managed using Flyway migrations to keep development and deployment environments consistent.

---

# Project Structure

```text
job-application-tracker/
│
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── ...
│   │   │   └── resources/
│   │   │       ├── db/
│   │   │       └── application.yml
│   │   └── test/
│   │       └── ...
│   │
│   └── pom.xml
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── views/
│   │   ├── services/
│   │   ├── stores/
│   │   └── ...
│   └── package.json
│
├── docs/
│   ├── architecture.md
│   ├── database.md
│   └── api.md
│
├── docker-compose.yml
├── SPEC.md
├── CLAUDE.md
└── README.md
```

---

# Future Improvements

Potential future features include:

* [ ] Email notifications
* [ ] Calendar integration
* [ ] Resume management
* [ ] Job description import
* [ ] AI-powered job description analysis
* [ ] Skill matching
* [ ] AI-generated interview questions
* [ ] Multi-language support
* [ ] Advanced analytics
* [ ] CI/CD pipeline
* [ ] Production deployment

---

# Project Goals

This project was designed to explore and demonstrate practical full-stack engineering concepts, including:

* REST API design
* Authentication and authorization
* Relational database design
* Transaction management
* Background processing
* Caching
* API validation
* Automated testing
* Containerization
* Frontend/backend integration
* Application monitoring and analytics
* Maintainable project architecture

The goal is not only to build a functional application, but also to apply software engineering practices that would be relevant to production systems.

---

# License

This project is available under the MIT License.
