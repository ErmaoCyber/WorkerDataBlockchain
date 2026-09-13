# Worker Data Platform

A full-stack platform for worker-controlled access to personal and professional data.

Employers can request access to specific worker information, workers can approve or reject those requests and define how long approved access remains valid, and important access events can be recorded on a blockchain-backed audit trail.

## Project background

This project was developed collaboratively as a team project.

Original team repository:

https://github.com/mussesseiniris/WorkerDataBlockchain

## Core workflow

```text
Employer creates access request
        |
        v
Worker reviews requested information
        |
        +--> Approve selected items
        |
        +--> Reject selected items
        |
        v
Worker sets access expiry
        |
        v
Employer can view approved, unexpired data
        |
        v
Notifications and audit events are recorded
```

## Architecture

```text
Browser
   |
   v
Next.js / React / TypeScript
   |
   | REST API + SignalR
   v
ASP.NET Core
   |
   +----------------------+----------------------+-------------------+
   |                      |                      |                   |
   v                      v                      v                   v
EF Core               SignalR / MediatR     Supabase Storage    Nethereum
   |                                                                  |
   v                                                                  v
PostgreSQL                                                       Hardhat / Solidity
```

PostgreSQL remains the source of truth for application data. The blockchain is used as an audit layer rather than as the primary data store.

Actual personal data values are not stored on-chain.

## Tech stack

### Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS
- SignalR client
- Jest
- React Testing Library

### Backend

- C#
- ASP.NET Core
- Entity Framework Core
- PostgreSQL
- JWT Bearer authentication
- MediatR
- SignalR
- Swagger / OpenAPI

### Storage and blockchain

- PostgreSQL hosted on Supabase
- Supabase Storage
- Solidity
- Hardhat
- Nethereum

### Development and CI

- Docker
- Docker Compose
- GitHub Actions
- xUnit
- Moq

## Key domain concepts

### Request

An employer creates a request describing why access is needed and which worker information is being requested.

### Permission

A request can contain multiple permissions. Each permission represents access to a specific field or data item and can be approved or rejected independently.

### Access expiry

The worker chooses the expiry date when approving access. Approved data is only available while the request remains active and unexpired.

### Audit trail

Events such as request creation, approval, rejection, data viewing, revocation, and review can be recorded as blockchain audit events.

## Real-time notifications

Notifications use MediatR for in-process application events and SignalR for real-time delivery to the frontend.

```text
Business action
   |
   v
NotificationCommand
   |
   v
MediatR
   |
   v
NotificationEvent
   |
   +--> Persist notification
   |
   +--> SignalR push
```

## My contributions

I worked across both frontend and backend, mainly on:

- Worker and employer dashboard development
- Active-access filtering and access-control workflows
- Worker-controlled access expiry
- Worker-facing audit views and blockchain audit integration
- Automated backend and frontend testing

## Repository structure

```text
wdb-frontend/       Next.js frontend
wdb-backend/        ASP.NET Core backend
wdb-backend.Tests/  Backend tests
wdb-blockchain/     Solidity / Hardhat project
.github/workflows/  GitHub Actions CI
docker-compose.yml
```

## Getting started

### Prerequisites

For the full application:

- Docker Desktop

For running tests directly:

- .NET SDK 10
- Node.js 18 or newer

### Clone

```bash
git clone https://github.com/ErmaoCyber/WorkerDataBlockchain.git
cd WorkerDataBlockchain
```

### Environment variables

Create a root `.env` file from the provided example:

```bash
cp .env.example .env
```

Configure the required PostgreSQL, Supabase Storage, and blockchain values locally.

Do not commit passwords, service-role credentials, or private keys.

### Run with Docker

```bash
docker compose up
```

Local services:

```text
Frontend: http://localhost:3000
Backend:  http://localhost:5258
Swagger:  http://localhost:5258/swagger
```

Rebuild after dependency or Dockerfile changes:

```bash
docker compose up --build
```

Stop the application:

```bash
docker compose down
```

## Testing

### Backend

From the repository root:

```bash
dotnet test
```

### Frontend

```bash
cd wdb-frontend
npm ci
npm test
```

## Continuous integration

GitHub Actions runs backend and frontend checks for pushes to `main` and pull requests targeting `main`.

Backend CI:

```text
restore -> build -> test
```

Frontend CI:

```text
npm ci -> npm test
```

The current workflow provides continuous integration only; production deployment is not automated by this repository.

## Engineering trade-offs

This project was built as a proof of concept, so some implementation choices are intentionally simpler than they would be in a production system.

- PostgreSQL is the primary source of business state; blockchain records audit metadata only.
- Blockchain availability is kept separate from core application functionality in parts of the system.
- The local blockchain setup is designed for development and demonstration rather than production gas efficiency.
- Some blockchain queries scan and filter events in application code, which would need a more scalable indexing strategy at higher volume.
- A production version would need stronger secrets management, retry and idempotency handling, deployment automation, and broader integration testing.
