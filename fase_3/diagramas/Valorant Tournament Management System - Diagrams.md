# Valorant Tournament Management System 🎮
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![Status](https://img.shields.io/badge/status-in%20development-yellow)

## Table of Contents
- [Valorant Tournament Management System 🎮](#valorant-tournament-management-system-)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Architecture \& Design](#architecture--design)
    - [System Architecture](#system-architecture)
    - [Entity-Relationship Diagram](#entity-relationship-diagram)
    - [Class Diagram](#class-diagram)
    - [Data Flow Diagram](#data-flow-diagram)
  - [Technology Stack](#technology-stack)
  - [Development Setup](#development-setup)
  - [Team \& Documentation](#team--documentation)

## Overview
A comprehensive platform for managing Valorant tournaments with:
- User authentication, team and players management 
- Tournament creation and bracket generation
- Match scheduling and statistics tracking

## Architecture & Design

### System Architecture

```mermaid

flowchart TB
  %% Camadas do Sistema
  subgraph "Camada de Apresentação"
    direction TB
    A1["⚡ Next.js + TypeScript"]
    A2["💅 Tailwind CSS"]
    A3["📦 Componentes UI"]
    A4["📁 Pages / App Router"]
    A5["📝 React Hook Form + Zod"]
    A6["🔐 Autenticação FE (JWT + Cookies)"]
    A7["📊 Componentes de Gráfico"]
  end

  subgraph "Camada de Aplicação"
    direction TB
    B1["🔄 Data Fetching (getStaticProps, SWR)"]
    B2["🗂️ State Management (Context API / Redux)"]
    B3["🔗 Custom Hooks"]
  end

  subgraph "Camada de API"
    direction TB
    C1["📡 Express.js API REST"]
    C2["🔐 Bcrypt + JWT"]
    C3["🧪 Middlewares (JWT + Zod/Joi)"]
  end

  subgraph "Camada de Persistência"
    direction TB
    D1["🧬 Sequelize ORM"]
    D2["📘 Models: Usuário, Equipe, Participante, …"]
  end

  subgraph "Camada de Dados"
    direction TB
    E1["🐘 PostgreSQL"]
  end

  subgraph "Infraestrutura"
    direction TB
    F1["🐳 Docker & Docker Compose"]
    F2["📄 .env"]
  end

  subgraph "Integrações Externas"
    direction TB
    G1["🌐 QuickChart API"]
  end

  %% Fluxos
  A4 --> B1
  A3 --> A4
  A5 --> B1
  A6 --> B1
  A7 --> B1
  B1 --> C1
  B2 --> A4
  B3 --> B1
  C1 --> C2
  C1 --> C3
  C1 --> D1
  C1 --> G1
  D1 --> D2
  D2 --> E1
  F1 --> C1
  F1 --> E1
  F2 --> C1

```

### Entity-Relationship Diagram

```mermaid
erDiagram
    User {
        int user_id PK
        string name
        string email UK
        string password
    }
    
    Team {
        int team_id PK
        string name UK
        int user_id FK
    }
    
    Participant {
        int participant_id PK
        string name
        string nickname
        date birth_date
        string phone
        int team_id FK
        boolean is_coach
        int user_id FK
    }
    
    Championship {
        int championship_id PK
        string name
        string description
        string format
        date start_date
        date end_date
        string location
        string status
        string prize
        int user_id FK
    }
    
    Match {
        int match_id PK
        int championship_id FK
        int teamA_id FK
        int teamB_id FK
        date date
        string stage
        enum bracket
        int winner_team_id FK
        jsonb score
        string map
        string status
        int next_match_id FK
    }
    
    Subscription {
        int subscription_id PK
        int championship_id FK
        int team_id FK
        date subscription_date
    }
    
    Agent {
        int agent_id PK
        string name
    }
    
    ParticipantStatistics {
        int statistic_id PK
        int match_id FK
        int team_id FK
        int participant_id FK
        int agent_id FK
        int kills
        int assists
        int deaths
        int spike_plants
        int spike_defuses
        boolean MVP
        float kda
        int average_combat_score
        int total_score
    }
    
    ChampionshipStatistics {
        int statistic_id PK
        int championship_id FK
        int participant_id FK
        int team_id FK
        int kills
        int assists
        int deaths
        int spike_plants
        int spike_defuses
        int MVPs
        int first_kills
    }

    %% Relacionamentos User
    User ||--o{ Championship : "cria"
    User ||--o{ Team : "possui"
    User ||--o{ Participant : "registra"
    
    %% Relacionamentos Team
    Team ||--o{ Participant : "possui"
    Team ||--o{ Subscription : "inscreve-se"
    Team ||--o{ ChampionshipStatistics : "possui"
    
    %% Relacionamentos Championship
    Championship ||--o{ Match : "contém"
    Championship ||--o{ Subscription : "recebe"
    Championship ||--o{ ChampionshipStatistics : "gera"
    
    %% Relacionamentos Match
    Match }o--|| Team : "TeamA"
    Match }o--|| Team : "TeamB"
    Match }o--o| Team : "Winner"
    Match ||--o{ ParticipantStatistics : "gera"
    Match }o--o| Match : "next_match"
    
    %% Relacionamentos Subscription (Many-to-Many)
    Team ||--o{ Subscription : ""
    Championship ||--o{ Subscription : ""
    
    %% Relacionamentos Participant
    Participant ||--o{ ParticipantStatistics : "possui"
    Participant ||--o{ ChampionshipStatistics : "possui"
    
    %% Relacionamentos Agent
    Agent ||--o{ ParticipantStatistics : "usado_em"
```
### Class Diagram 
```mermaid
classDiagram
    direction LR

    class User {
        +int userId
        +string name
        +string email
        +string password
        +List~Championship~ championships
        +List~Team~ teams
        +List~Participant~ participants
        +createChampionship()
        +createTeam()
        +createParticipant()
    }

    class Team {
        +int teamId
        +string name
        +int userId
        +List~Participant~ participants
        +List~Subscription~ subscriptions
        +List~ChampionshipStatistics~ statistics
        +addParticipant()
        +subscribeToChampionship()
    }

    class Participant {
        +int participantId
        +string name
        +string nickname
        +date birthDate
        +string phone
        +boolean isCoach
        +int teamId
        +int userId
        +List~ParticipantStatistics~ matchStatistics
        +List~ChampionshipStatistics~ championshipStatistics
    }

    class Championship {
        +int championshipId
        +string name
        +string description
        +string format
        +date startDate
        +date endDate
        +string location
        +string status
        +string prize
        +int userId
        +List~Match~ matches
        +List~Subscription~ subscriptions
        +List~ChampionshipStatistics~ statistics
        +scheduleMatch()
        +addSubscription()
    }

    class Match {
        +int matchId
        +int championshipId
        +int teamAId
        +int teamBId
        +date date
        +string stage
        +string bracket
        +int winnerTeamId
        +jsonb score
        +string map
        +string status
        +int nextMatchId
        +List~ParticipantStatistics~ statistics
        +setWinner()
        +setNextMatch()
    }

    class Subscription {
        +int subscriptionId
        +int championshipId
        +int teamId
        +date subscriptionDate
        +confirm()
        +cancel()
    }

    class Agent {
        +int agentId
        +string name
        +List~ParticipantStatistics~ statistics
    }

    class ParticipantStatistics {
        +int statisticId
        +int matchId
        +int teamId
        +int participantId
        +int agentId
        +int kills
        +int assists
        +int deaths
        +int spikePlants
        +int spikeDefuses
        +boolean mvp
        +float kda
        +int averageCombatScore
        +int totalScore
        +calculateKDA()
    }

    class ChampionshipStatistics {
        +int statisticId
        +int championshipId
        +int participantId
        +int teamId
        +int kills
        +int assists
        +int deaths
        +int spikePlants
        +int spikeDefuses
        +int mvps
        +int firstKills
        +updateStats()
    }

    %% Relacionamentos
    User "1" --> "0..*" Championship : cria
    User "1" --> "0..*" Team : possui
    User "1" --> "0..*" Participant : registra
    
    Team "1" --> "0..*" Participant : possui
    Team "1" --> "0..*" Subscription : inscreve-se
    Team "1" --> "0..*" ChampionshipStatistics : possui
    
    Championship "1" --> "0..*" Match : contém
    Championship "1" --> "0..*" Subscription : recebe
    Championship "1" --> "0..*" ChampionshipStatistics : gera
    
    Match "1" --> "1" Team : TeamA
    Match "1" --> "1" Team : TeamB
    Match "1" --> "0..1" Team : Winner
    Match "1" --> "0..*" ParticipantStatistics : gera
    Match "1" --> "0..1" Match : nextMatch
    
    Participant "1" --> "0..*" ParticipantStatistics : possui
    Participant "1" --> "0..*" ChampionshipStatistics : possui
    
    Agent "1" --> "0..*" ParticipantStatistics : usado_em

```
### Data Flow Diagram

```mermaid
sequenceDiagram
    participant Client
    participant Routes
    participant Middleware
    participant Controller
    participant Service
    participant Model
    participant Database
    
    Client->>Routes: HTTP Request
    Routes->>Middleware: Validate/Auth
    Middleware->>Controller: Process Request
    Controller->>Service: Business Logic
    Service->>Model: Data Operations
    Model->>Database: SQL Query
    Database-->>Model: Result
    Model-->>Service: Formatted Data
    Service-->>Controller: Response Data
    Controller-->>Routes: JSON Response
    Routes-->>Client: HTTP Response
```


## Technology Stack

### Frontend Architecture
- **Framework:** Next.js v15.3.2  
- **Library:** React v19.0.0 + React-DOM v19.0.0  
- **Styling:** Tailwind CSS v4.1.5 + PostCSS v8.5.3  
- **Linting:** ESLint v9.x + `eslint-config-next` v15.3.2  
- **TypeScript:** v5.x  
- **Package Manager:** npm

### Backend Architecture
- **Runtime:** Node.js v20.x (ESM)  
- **Framework:** Express.js v5.1.0  
- **ORM:** Sequelize v6.37.7 with `pg` v8.15.6 + `pg-hstore` v2.3.4  
- **Auth & Security:** `jsonwebtoken` v9.0.2 + `bcryptjs` v3.0.2  
- **Validation:** Zod v3.24.4  
- **Env Management:** dotenv v16.5.0  
- **Dev Tooling:** nodemon v3.1.10

## Development Setup

### Quick Start

# Clone repositories

```bash
git clone https://github.com/Matari73/Backend-CCE-AS66A
git clone https://github.com/Pedroooxx/frontend-grupo-a-cce
```

# Backend setup
```bash
cd Backend-CCE-AS66A
cp .env.example .env
docker compose down
docker compose up --build
```

# Frontend setup

```bash
cd ../frontend-grupo-a-cce
npm install
cp .env.example .env.local
npm run dev
```
## Team 
- **Backend Team**: Mariana, Giovana, Ítalo
- **Frontend Team**: Pedro, Sérgio
```
