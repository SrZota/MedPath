# Arquitetura

| Campo | Valor |
|-------|-------|
| Código | ARCH-001 |
| Versão | 0.2.0 |
| Status | Aprovado (com decisões em aberto, ver final) |
| Última atualização | 12/10/2026 |

---

## Objetivo

Descrever a arquitetura do MedPath: organização da solução, módulos, camadas, padrões e tecnologias. Cada decisão relevante tem um ADR em [Adr/](Adr/), e o índice está em [07-Decisions.md](07-Decisions.md).

---

## Visão Geral

```text
┌──────────────────────────────┐
│   Cliente Web (responsivo)   │   ← tecnologia a definir (Fase 1, final)
└──────────────┬───────────────┘
               │ HTTPS / JSON  (/api/v1)
               ▼
┌──────────────────────────────────────────────┐
│              MedPath.Api (host)              │
│  ASP.NET Core · OpenAPI · Auth · ProblemDetails │
├──────────────────────────────────────────────┤
│               Monólito Modular               │
│  ┌────────────┐ ┌──────────┐ ┌────────────┐  │
│  │ Facilities │ │ Identity │ │ Engagement │  │
│  │   (MVP)    │ │  (MVP)   │ │    (F2)    │  │
│  └────────────┘ └──────────┘ └────────────┘  │
└──────────────┬───────────────────────────────┘
               ▼
┌──────────────────────────────┐
│ SQL Server — um schema por   │
│ módulo (facilities, identity)│
└──────────────────────────────┘
```

Um único processo é implantado (monólito) e o código é dividido em módulos isolados (modular). Veja o [ADR-002](Adr/ADR-002-modular-monolith.md).

---

## Módulos

| Módulo | Responsabilidade | Fase |
|--------|------------------|------|
| **Facilities** | Catálogo de unidades de saúde, especialidades, convênios, importação do CNES e busca por proximidade. | MVP |
| **Identity** | Usuários, autenticação (JWT) e perfis de acesso. | MVP |
| **Engagement** | Favoritos e avaliações de unidades. | F2 |
| **Institutions** | Gestores de instituições e reivindicação de perfil. | F3 |
| **Scheduling** | Agendamento. | F5 |

> **Por que a busca fica dentro de Facilities?** Ela lê exatamente os dados que o módulo mantém. Separar a busca em outro módulo agora criaria comunicação entre módulos sem nenhum ganho. Se no futuro ela exigir outra tecnologia (ex.: Elasticsearch), o assunto volta como um novo ADR.

---

## Camadas de cada módulo (Clean Architecture)

```text
Presentation  →  Application  →  Domain
                      ↑
               Infrastructure
```

| Camada | Contém | Pode depender de |
|--------|--------|------------------|
| **Domain** | Entidades, Value Objects, regras de negócio, interfaces de repositório | Nada (nem EF Core, nem ASP.NET) |
| **Application** | Casos de uso (commands/queries), DTOs, validações, interfaces de serviços | Domain |
| **Infrastructure** | EF Core, DbContext, migrations, repositórios, importador CNES | Application, Domain |
| **Presentation** | Endpoints HTTP do módulo | Application |

**Regra de ouro:** as dependências apontam para dentro. O Domain não conhece nenhuma das outras camadas. Veja o [ADR-003](Adr/ADR-003-clean-architecture.md).

A forma de separar essas camadas em projetos `.csproj` está em discussão no [ADR-013](Adr/ADR-013-project-granularity.md).

---

## Comunicação entre módulos

1. Um módulo **nunca** acessa as tabelas, o DbContext ou as entidades de outro módulo.
2. Quando um módulo precisa de outro, ele usa um **contrato público** (interface/DTO) exposto pelo outro módulo, por exemplo `Facilities.Contracts`.
3. A comunicação assíncrona (eventos de integração) será introduzida apenas quando houver necessidade real (previsão: F2).

---

## Persistência

- SQL Server com EF Core, abordagem Code First e Migrations ([ADR-005](Adr/ADR-005-sqlserver-efcore.md)).
- **Um DbContext e um schema por módulo**, o que garante o isolamento descrito acima.
- Coordenadas armazenadas como `geography` (SRID 4326) via NetTopologySuite ([ADR-006](Adr/ADR-006-geolocation.md)).
- Detalhes em [05-Database.md](05-Database.md).

---

## Segurança

- Autenticação JWT com perfis (`Admin` no MVP, `Patient` na F2) ([ADR-008](Adr/ADR-008-authentication-jwt.md)).
- Endpoints de busca são públicos; endpoints `/admin` exigem o perfil `Admin`.
- Segredos nunca ficam no repositório: em desenvolvimento usar `dotnet user-secrets`, em produção variáveis de ambiente ou um cofre.
- Rate limiting nos endpoints públicos (o ASP.NET Core tem middleware nativo).

---

## Tratamento de erros

- Respostas de erro no padrão **ProblemDetails** (RFC 9457), nativo do ASP.NET Core ([ADR-009](Adr/ADR-009-problem-details.md)).
- Um handler global (`IExceptionHandler`) converte exceções em respostas padronizadas. Stack trace nunca é exposto.

---

## Observabilidade

- Logs estruturados com Serilog, com `CorrelationId` em todas as requisições.
- Health check em `/health` (inclui a conexão com o banco).

---

## Tecnologias

| Tecnologia | Finalidade | ADR |
|------------|------------|-----|
| .NET 10 (LTS) / C# 14 | Plataforma | [ADR-004](Adr/ADR-004-dotnet-10.md) |
| ASP.NET Core | API REST | — |
| OpenAPI (`Microsoft.AspNetCore.OpenApi`) + Scalar ou Swagger UI | Documentação da API | — |
| EF Core + SQL Server | Persistência | [ADR-005](Adr/ADR-005-sqlserver-efcore.md) |
| NetTopologySuite | Dados geográficos | [ADR-006](Adr/ADR-006-geolocation.md) |
| FluentValidation | Validação de entrada | — |
| Serilog | Logs | — |
| xUnit + Testcontainers | Testes | [11-Testing-Strategy.md](11-Testing-Strategy.md) |
| Docker | SQL Server local e, futuramente, deploy | [12-Deployment.md](12-Deployment.md) |
| GitHub Actions | CI | [12-Deployment.md](12-Deployment.md) |

---

## Decisões em aberto (decidir junto com o desenvolvedor)

| ADR | Pergunta | Necessária antes de |
|-----|----------|---------------------|
| [ADR-012](Adr/ADR-012-endpoints-style.md) | Controllers ou Minimal APIs? | Sessão 2 |
| [ADR-013](Adr/ADR-013-project-granularity.md) | Quantos projetos por módulo? | Sessão 2 |
| [ADR-014](Adr/ADR-014-use-case-dispatch.md) | Como os endpoints chamam os casos de uso: MediatR ou handlers próprios? | Sessão 6 |
