# Repositório Central - Grupo A (AS66A-N16)
Este repositório reúne os documentos e artefatos produzidos durante o desenvolvimento de um sistema computacional para gestão de campeonatos do jogo Valorant, como parte da disciplina Certificadora de Competência Específica (CCE) do sexto período do curso de Análise e Desenvolvimento de Sistemas (UTFPR).

A disciplina tem como objetivo integrar os conhecimentos adquiridos ao longo da formação, por meio do desenvolvimento de um sistema que resolva um problema real. Além da entrega do produto final, o projeto avalia competências como gestão de projetos, levantamento de requisitos com cliente, modelagem de software, uso de ferramentas colaborativas, documentação, e aplicação de metodologias contemporâneas de desenvolvimento.

## Sobre o Projeto

A solução proposta pelo Grupo A é uma plataforma web para gerenciar campeonatos de Valorant, que centraliza o cadastro de jogadores e equipes, organiza partidas e fornece relatórios e dashboards informativos. O sistema conta com diferentes interfaces para organizadores e para o público geral, promovendo uma experiência completa e acessível.

**Funcionalidades Principais:**

- Cadastro e Gerenciamento: Registro de jogadores, equipes e campeonatos.
- Gestão de Campeonatos: Configuração de torneios com diferentes formatos (eliminatória simples e dupla).
- Automatização e Relatórios: Classificação automática e visualizações com dashboards interativos.
- Interfaces Diferenciadas: Áreas administrativas e públicas voltadas para organizadores e fãs.

**Repositórios Relacionados:**
- Repositório Backend: https://github.com/Matari73/Backend-CCE-AS66A
- Repositório Frontend: https://github.com/Pedroooxx/frontend-grupo-a-cce 

## Diagrama de Entidade Relacionamento

```mermaid
erDiagram
    USER ||--o{ CHAMPIONSHIP : creates
    USER ||--o{ TEAM : manages
    USER ||--o{ PARTICIPANT : creates
    TEAM ||--o{ PARTICIPANT : has
    CHAMPIONSHIP ||--o{ MATCH : has
    CHAMPIONSHIP ||--o{ SUBSCRIPTION : receives
    TEAM ||--o{ SUBSCRIPTION : makes
    MATCH ||--o{ PARTICIPANT_STATISTICS : generates
    PARTICIPANT ||--o{ PARTICIPANT_STATISTICS : participates_in
    AGENT ||--o{ PARTICIPANT_STATISTICS : used_in
    CHAMPIONSHIP ||--o{ CHAMPIONSHIP_STATISTICS : generates

    USER {
        int user_id
        string name
        string email
        string password
        boolean isOrganizador
    }

    TEAM {
        int team_id
        string name
        int ranking
        int user_id
    }

    PARTICIPANT {
        int participant_id
        string name
        string nickname
        datetime birth_date
        int phone
        int team_id
        boolean is_COACH
        int user_id
    }

    CHAMPIONSHIP {
        int championship_id
        string name
        string description
        string format
        date start_date
        date end_date
        string location
        string status
        int user_id
    }

    MATCH {
        int match_id
        int championship_id
        int teamA_id
        int teamB_id
        date date
        string stage
        int winner_team_id
        dict score
        string map
    }

    SUBSCRIPTION {
        int subscription_id
        int championship_id
        int team_id
        date subscription_date
        string status
    }

    PARTICIPANT_STATISTICS {
        int statistic_id
        int match_id
        int participant_id
        int agent_id
        int kills
        int assists
        int deaths
        int spike_plants
        int spike_defuses
        boolean MVP
        boolean first_kill
        boolean first_defuse
    }

    AGENT {
        int agent_id
        string name
    }

    CHAMPIONSHIP_STATISTICS {
        int statistic_id
        int championship_id
        int participant_id
        int team_id
        int kills
        int assists
        int deaths
        int spike_plants
        int spike_defuses
        int MVPs
        int first_kills
    }
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
