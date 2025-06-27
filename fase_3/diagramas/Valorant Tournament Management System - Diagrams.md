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
    - [Data Flow](#data-flow)
    - [Key Relationships](#key-relationships)
  - [Technology Stack](#technology-stack)
  - [Development Setup](#development-setup)
  - [Team \& Documentation](#team--documentation)

## Overview
A comprehensive platform for managing Valorant tournaments with:
- User authentication and team management
- Tournament creation and bracket generation
- Match scheduling and statistics tracking
- Performance analytics and reporting

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
        boolean first_kill
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

### Data Flow

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

### Key Relationships

### 1. **User** (Usuário Central)

- Cria e gerencia **Championships**
- Possui **Teams**
- Registra **Participants**

### 2. **Championship** (Campeonato)

- Contém múltiplas **Matches**
- Recebe **Subscriptions** de teams
- Gera **ChampionshipStatistics**

### 3. **Team** (Equipe)

- Possui múltiplos **Participants**
- Participa de **Championships** via **Subscription**
- Joga **Matches** como TeamA, TeamB ou Winner

### 4. **Match** (Partida)

- Liga dois **Teams** (TeamA e TeamB)
- Pode ter um **Winner Team**
- Gera **ParticipantStatistics**
- Pode referenciar uma **próxima partida** (bracket system)

### 5. **Participant** (Participante)

- Pertence a um **Team**
- Possui **ParticipantStatistics** por partida
- Possui **ChampionshipStatistics** agregadas

### 6. **Statistics** (Estatísticas)

- **ParticipantStatistics**: Estatísticas por partida
- **ChampionshipStatistics**: Estatísticas agregadas por campeonato

### 7. **Agent** (Agente do Valorant)

- Utilizado nas **ParticipantStatistics**
- Representa os personagens jogáveis

## Tipos de Relacionamento

- **1:N** - Um para muitos (ex: User → Teams)
- **N:M** - Muitos para muitos via tabela intermediária (Team ↔ Championship via Subscription)
- **Self-Reference** - Autoreferência (Match → next_match)
- **Multiple References** - Múltiplas referências para a mesma tabela (Match → Team como TeamA, TeamB, Winner)

## Technology Stack

| Layer          | Technologies                          |
|----------------|---------------------------------------|
| **Frontend**   | Next.js, Tailwind CSS, React Hook Form|
| **Backend**    | Express.js, Sequelize ORM             |
| **Database**   | PostgreSQL                            |
| **DevOps**     | Docker, CI/CD pipelines               |

## Development Setup

```bash
# Backend
docker compose up --build

# Frontend
npm install && npm run dev
```

## Team & Documentation
- **Backend Team**: Mariana, Giovana, Italo
- **Frontend Team**: Pedro, Sérgio
- **API Docs**: [Swagger UI](#) | [Postman Collection](#)
```
