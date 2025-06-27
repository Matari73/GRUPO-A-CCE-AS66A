# Repositório Central - Grupo A (AS66A-N16)
Este repositório reúne os documentos e artefatos produzidos durante o desenvolvimento de um sistema computacional para gestão de campeonatos do jogo Valorant, como parte da disciplina Certificadora de Competência Específica (CCE) do sexto período do curso de Análise e Desenvolvimento de Sistemas (UTFPR).

A disciplina tem como objetivo integrar os conhecimentos adquiridos ao longo da formação, por meio do desenvolvimento de um sistema que resolva um problema real. Além da entrega do produto final, o projeto avalia competências como gestão de projetos, levantamento de requisitos com cliente, modelagem de software, uso de ferramentas colaborativas, documentação, e aplicação de metodologias contemporâneas de desenvolvimento.

## Sobre o Projeto

A solução proposta pelo Grupo A é uma plataforma web para gerenciar campeonatos de Valorant, que centraliza o cadastro de jogadores e equipes, organiza partidas e fornece relatórios e dashboards informativos. O sistema conta com diferentes interfaces para organizadores e para o público geral, promovendo uma experiência completa e acessível.

**Funcionalidades Principais:**

- Cadastro e Gerenciamento: Registro de jogadores, equipes e campeonatos.
- Gestão de Campeonatos: Configuração de torneios com diferentes formatos (eliminatória simples e dupla).
- Automatização e Paineis: Classificação automática (sistema de chaveamento) e paineis com os dados estatísticos do andamento do campeonato.
- Interfaces Diferenciadas: Áreas administrativas e públicas voltadas para organizadores e fãs.

**Repositórios Relacionados:**
- Repositório Backend: https://github.com/Matari73/Backend-CCE-AS66A
- Repositório Frontend: https://github.com/Pedroooxx/frontend-grupo-a-cce 

## Diagrama de Entidade Relacionamento

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

## Diagrama de Arquitetura
```mermaid
flowchart TB
  %% Camadas do Sistema Frontend (Repositório Separado)
  subgraph "Camada de Apresentação"
    direction TB
    A1["⚡ Next.js + TypeScript"]
    A2["💅 Tailwind CSS"]
    A3["📦 Componentes UI"]
    A4["📁 Pages / App Router"]
    A5["📝 React Hook Form + Zod"]
    A6["🔐 Autenticação FE (JWT + Cookies)"]
  end

  subgraph "Camada de Aplicação Frontend"
    direction TB
    B1["🔄 Data Fetching (getStaticProps, SWR)"]
    B2["🗂️ State Management (Context API / Redux)"]
    B3["🔗 Custom Hooks"]
  end

  %% Camadas do Sistema Backend (Repositório Atual)
  subgraph "Camada de API/Rotas Backend"
    direction TB
    C1["📡 Express.js Server"]
    C2["🔗 CORS Middleware"]
    C3["📄 JSON Parser"]
    C4["📋 Swagger Documentation"]
    C5["🛣️ Route Handlers"]
  end

  subgraph "Camada de Autenticação & Segurança"
    direction TB
    D1["🔐 BCrypt.js (Hash)"]
    D2["🎫 JWT (JsonWebToken)"]
    D3["🛡️ Auth Middleware"]
    D4["👑 Ownership Middleware"]
  end

  subgraph "Camada de Validação"
    direction TB
    E1["✅ Zod Schema Validation"]
    E2["🧪 ValidateSchema Middleware"]
    E3["📝 Request/Response Schemas"]
  end

  subgraph "Camada de Controllers"
    direction TB
    F1["👤 User Controller"]
    F2["🏆 Championship Controller"]
    F3["👥 Team Controller"]
    F4["🤖 Agent Controller"]
    F5["⚔️ Match Controller"]
    F6["📊 Statistics Controllers"]
    F7["📝 Subscription Controller"]
    F8["🎮 Participant Controller"]
  end

  subgraph "Camada de Services"
    direction TB
    G1["🏆 Championship Service"]
    G2["🥇 Single Elimination Service"]
    G3["🥈 Double Elimination Service"]
    G4["✅ Subscription Validation"]
    G5["👥 Team Validation"]
  end

  subgraph "Camada de Persistência"
    direction TB
    H1["🧬 Sequelize ORM"]
    H2["🔗 Model Associations"]
    H3["📋 Models"]
  end

  subgraph "Modelos de Dados"
    direction TB
    I1["👤 User Model"]
    I2["🏆 Championship Model"]
    I3["👥 Team Model"]
    I4["🤖 Agent Model"]
    I5["⚔️ Match Model"]
    I6["🎮 Participant Model"]
    I7["📊 Statistics Models"]
    I8["📝 Subscription Model"]
  end

  subgraph "Camada de Dados"
    direction TB
    J1["🐘 PostgreSQL Database"]
  end

  subgraph "Infraestrutura"
    direction TB
    K1["🐳 Docker & Docker Compose"]
    K2["📄 Environment Variables (.env)"]
    K3["🔄 Nodemon (Development)"]
    K4["🌱 Database Seeding"]
  end

  subgraph "Scripts & Utilitários"
    direction TB
    L1["🌱 Seed Script"]
    L2["📊 Bracket Utils"]
    L3["🔄 Database Sync"]
  end

  %% Fluxos Frontend para Backend
  A4 --> B1
  A3 --> A4
  A5 --> B1
  A6 --> B1
  A7 --> B1
  B1 --> C1
  B2 --> A4
  B3 --> B1

  %% Fluxos Internos do Backend
  C1 --> C2
  C1 --> C3
  C1 --> C4
  C1 --> C5
  C5 --> D3
  C5 --> E2
  D3 --> D4
  E2 --> E1

  %% Controllers e Services
  C5 --> F1
  C5 --> F2
  C5 --> F3
  C5 --> F4
  C5 --> F5
  C5 --> F6
  C5 --> F7
  C5 --> F8

  F1 --> G4
  F2 --> G1
  F3 --> G5
  F5 --> G2
  F5 --> G3
  G1 --> L2

  %% Persistência
  F1 --> H1
  F2 --> H1
  F3 --> H1
  F4 --> H1
  F5 --> H1
  F6 --> H1
  F7 --> H1
  F8 --> H1

  H1 --> H2
  H1 --> H3
  H3 --> I1
  H3 --> I2
  H3 --> I3
  H3 --> I4
  H3 --> I5
  H3 --> I6
  H3 --> I7
  H3 --> I8

  %% Banco de Dados
  I1 --> J1
  I2 --> J1
  I3 --> J1
  I4 --> J1
  I5 --> J1
  I6 --> J1
  I7 --> J1
  I8 --> J1

  %% Infraestrutura
  K1 --> C1
  K1 --> J1
  K2 --> C1
  K3 --> C1
  K4 --> L1
  L1 --> H1
  L3 --> H1

  %% Autenticação e Segurança
  D1 --> D2
  D2 --> D3

  %% Validação
  E3 --> E1
  E1 --> E2


  class A1,A2,A3,A4,A5,A6,A7 frontendLayer
  class B1,B2,B3 frontendAppLayer
  class C1,C2,C3,C4,C5 apiLayer
  class D1,D2,D3,D4 authLayer
  class E1,E2,E3 validationLayer
  class F1,F2,F3,F4,F5,F6,F7,F8 controllerLayer
  class G1,G2,G3,G4,G5,G6 serviceLayer
  class H1,H2,H3,I1,I2,I3,I4,I5,I6,I7,I8 persistenceLayer
  class J1 dataLayer
  class K1,K2,K3,K4,L1,L2,L3 infraLayer
  class M1,M2 externalLayer
```
