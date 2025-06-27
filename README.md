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
