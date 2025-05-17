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
    CLIENTES {
      INT id_cliente PK
      VARCHAR(40) nome
      VARCHAR(12) cpf
      VARCHAR(12) telefone
      VARCHAR(100) email
      VARCHAR(255) endereco
    }
    SERVICOS {
      INT id_servico PK
      VARCHAR(45) nome_servico
      VARCHAR(45) pacote
      TIME duracao
      FLOAT preco
    }
    FUNCIONARIOS {
      INT id_funcionario PK
      VARCHAR(45) nome
    }
    AGENDAMENTOS {
      INT id_agendamento PK
      INT id_cliente FK
      INT id_servico FK
      DATE dia
      TIME horario
      INT qtd_participantes
      VARCHAR(20) status 
      INT id_metodo_pagamento FK
    }
    PROCEDIMENTOS {
      INT id_procedimento PK
      INT id_agendamento FK
      BOOLEAN adicional
      VARCHAR(45) descricao_adicional
      FLOAT valor_adicional
      FLOAT desconto
      FLOAT valor_final
    }
    PROCEDIMENTO_FUNCIONARIOS {
      INT id_procedimento FK
      INT id_funcionario FK
    }
    METODOS_PAGAMENTO {
      INT id_metodo PK
      VARCHAR(50) metodo_nome
    }
    COMISSOES {
      INT id_comissao PK
      INT id_funcionario FK
      INT id_procedimento FK
      FLOAT percentual_comissao
      FLOAT valor_comissao
    }
    FEEDBACKS {
      INT id_feedback PK
      INT id_agendamento FK
      INT nota 
      TEXT comentario
    }
    PROMOCOES {
      INT id_promocao PK
      VARCHAR(100) nome_promocao
      TEXT descricao
      FLOAT desconto_percentual
      DATE data_inicio
      DATE data_fim
    }
    SERVICOS_PROMOCOES {
      INT id_servico FK
      INT id_promocao FK
    }

    CLIENTES ||--o{ AGENDAMENTOS : "realiza"
    SERVICOS ||--o{ AGENDAMENTOS : "oferece"
    AGENDAMENTOS ||--|| PROCEDIMENTOS : "gera"
    PROCEDIMENTOS ||--o{ PROCEDIMENTO_FUNCIONARIOS : "associa"
    FUNCIONARIOS ||--o{ PROCEDIMENTO_FUNCIONARIOS : "atua em"
    METODOS_PAGAMENTO ||--o{ AGENDAMENTOS : "utiliza"
    FUNCIONARIOS ||--o{ COMISSOES : "recebe"
    PROCEDIMENTOS ||--o{ COMISSOES : "gera"
    AGENDAMENTOS ||--o{ FEEDBACKS : "avalia"
    PROMOCOES ||--o{ SERVICOS_PROMOCOES : "aplica"
    SERVICOS ||--o{ SERVICOS_PROMOCOES : "pertence"
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
