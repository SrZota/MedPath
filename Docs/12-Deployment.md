# Decisões Arquiteturais

## Objetivo

Este documento consolida as principais decisões arquiteturais adotadas no MedPath.

Seu objetivo é fornecer uma visão centralizada das escolhas realizadas durante o desenvolvimento do projeto, facilitando o entendimento da arquitetura e servindo como ponto de referência para desenvolvedores, arquitetos e mantenedores.

Cada decisão detalhada possui um ADR correspondente localizado na pasta **Adr**.

---

# Como Ler Este Documento

Cada decisão apresenta:

- O problema que motivou a escolha;
- A decisão adotada;
- A justificativa;
- O impacto esperado;
- O ADR correspondente.

---

# Resumo das Decisões

| Código | Decisão | Status |
|---------|---------|--------|
| ADR-001 | API First | ✅ Aceita |
| ADR-002 | Modular Monolith | ✅ Aceita |
| ADR-003 | Clean Architecture | ✅ Aceita |
| ADR-004 | SQL Server | ⏳ Planejada |
| ADR-005 | JWT | ⏳ Planejada |
| ADR-006 | Entity Framework Core | ⏳ Planejada |
| ADR-007 | FluentValidation | ⏳ Planejada |
| ADR-008 | Serilog | ⏳ Planejada |
| ADR-009 | Versionamento da API | ⏳ Planejada |

---

# ADR-001 — API First

## Problema

O sistema será consumido por uma aplicação Web e, futuramente, poderá ser integrado com outras aplicações.

## Decisão

Adotar a abordagem **API First**, desenvolvendo os contratos da API antes da implementação das funcionalidades.

## Justificativa

- Facilita integrações.
- Permite evolução independente do frontend.
- Define contratos claros entre cliente e servidor.

## Impacto

Todas as funcionalidades deverão ser expostas através de APIs REST documentadas.

---

# ADR-002 — Modular Monolith

## Problema

Era necessário definir uma arquitetura que permitisse crescimento sem introduzir a complexidade operacional dos microsserviços.

## Decisão

Adotar uma arquitetura **Monólito Modular**.

## Justificativa

- Simplicidade de implantação.
- Menor custo operacional.
- Separação clara entre módulos.
- Facilidade para futura migração para microsserviços.

## Impacto

Cada módulo possuirá responsabilidade própria e dependerá de contratos bem definidos.

---

# ADR-003 — Clean Architecture

## Problema

Era necessário manter as regras de negócio independentes de frameworks e tecnologias.

## Decisão

Adotar a **Clean Architecture**.

## Justificativa

- Baixo acoplamento.
- Alta testabilidade.
- Independência tecnológica.
- Facilidade de manutenção.

## Impacto

O domínio permanecerá isolado da infraestrutura.

---

# ADR-004 — SQL Server

## Problema

Era necessário definir um banco de dados relacional confiável para suportar o domínio da aplicação.

## Decisão

Utilizar SQL Server.

## Justificativa

- Excelente integração com o ecossistema .NET.
- Suporte completo ao Entity Framework Core.
- Recursos avançados de segurança e desempenho.

## Impacto

Toda a persistência será baseada em banco de dados relacional.

**Status:** Planejado.

---

# ADR-005 — JWT

## Problema

Era necessário definir uma estratégia de autenticação stateless para a API.

## Decisão

Utilizar JSON Web Token (JWT).

## Justificativa

- Escalabilidade.
- Independência de sessão.
- Compatibilidade com APIs REST.

## Impacto

A autenticação será baseada em tokens assinados.

**Status:** Planejado.

---

# ADR-006 — Entity Framework Core

## Problema

Era necessário definir uma estratégia para persistência de dados.

## Decisão

Utilizar Entity Framework Core como ORM.

## Justificativa

- Integração nativa com .NET.
- Suporte a Migrations.
- Fluent API.
- Alto nível de produtividade.

## Impacto

Todo acesso ao banco será realizado através da camada Infrastructure.

**Status:** Planejado.

---

# ADR-007 — FluentValidation

## Problema

Era necessário padronizar a validação das entradas da aplicação.

## Decisão

Utilizar FluentValidation.

## Justificativa

- Separação das regras de validação.
- Código mais limpo.
- Fácil manutenção.

## Impacto

As validações serão executadas na camada Application.

**Status:** Planejado.

---

# ADR-008 — Serilog

## Problema

Era necessário definir uma estratégia para registro estruturado de logs.

## Decisão

Utilizar Serilog.

## Justificativa

- Logs estruturados.
- Integração com diversos provedores.
- Facilidade para auditoria e monitoramento.

## Impacto

Todos os eventos importantes da aplicação deverão ser registrados.

**Status:** Planejado.

---

# ADR-009 — Versionamento da API

## Problema

Era necessário garantir evolução da API sem quebrar integrações existentes.

## Decisão

Versionar a API.

## Justificativa

- Compatibilidade entre versões.
- Evolução controlada.
- Menor impacto para consumidores.

## Impacto

Os endpoints seguirão o padrão:

```text
/api/v1/
```

**Status:** Planejado.

---

# Diretrizes para Novas Decisões

Toda nova decisão arquitetural deverá:

- Resolver um problema identificado.
- Avaliar alternativas.
- Registrar justificativa.
- Documentar impactos.
- Possuir um ADR correspondente.

---

# Histórico

| Data | ADR | Descrição |
|------|-----|-----------|
| 2026 | ADR-001 | API First |
| 2026 | ADR-002 | Modular Monolith |
| 2026 | ADR-003 | Clean Architecture |
| 2026 | ADR-004 | SQL Server |
| 2026 | ADR-005 | JWT |
| 2026 | ADR-006 | Entity Framework Core |
| 2026 | ADR-007 | FluentValidation |
| 2026 | ADR-008 | Serilog |
| 2026 | ADR-009 | Versionamento da API |

---

# Considerações

As decisões registradas neste documento representam o estado atual da arquitetura do MedPath.

Sempre que uma decisão for alterada, um novo ADR deverá ser criado, preservando o histórico e garantindo a rastreabilidade das escolhas arquiteturais do projeto.