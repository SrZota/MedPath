# MedPath

> **"Estou precisando de atendimento agora. Para onde eu devo ir?"**

O MedPath é uma plataforma de navegação em saúde: a partir da localização do usuário, encontra as unidades de saúde mais adequadas (hospital, UPA, UBS, clínica), filtrando por especialidade, convênio e horário de funcionamento, e abre a rota até lá. Não exige cadastro, porque ninguém deveria criar conta no meio de uma emergência.

O projeto é a evolução do TCC **"Cadê Hospital?"** (ETEC Prof. Horácio Augusto da Silveira, 2018), reconstruído como produto em .NET.

> ⚠️ O MedPath não faz diagnóstico nem triagem. Em caso de emergência, ligue **192 (SAMU)**.

---

## Status

🚧 **Fase 0 — Fundação técnica.** Veja o [Roadmap](Docs/06-Roadmap.md) e o [Backlog](Docs/Backlog.md).

## Stack

.NET 10 · ASP.NET Core · EF Core · SQL Server (`geography`) · NetTopologySuite · xUnit + Testcontainers · Docker · GitHub Actions

Arquitetura: **monólito modular** com **Clean Architecture** em cada módulo. Veja [Arquitetura](Docs/03-Architecture.md).

## Documentação

| Documento | Conteúdo |
|-----------|----------|
| [01 — Visão](Docs/01-Vision.md) | Problema, público, proposta de valor, escopo do MVP |
| [02 — Requisitos](Docs/02-Requirements.md) | Requisitos funcionais, não funcionais e regras de negócio |
| [03 — Arquitetura](Docs/03-Architecture.md) | Módulos, camadas e tecnologias |
| [04 — Domínio](Docs/04-Domain.md) | Linguagem ubíqua, agregados e value objects |
| [05 — Banco de Dados](Docs/05-Database.md) | Modelo, convenções e índices |
| [06 — Roadmap](Docs/06-Roadmap.md) | Fases do produto |
| [07 — Decisões (ADRs)](Docs/07-Decisions.md) | Índice das decisões arquiteturais |
| [08 — Padrões de API](Docs/08-Api-Standards.md) | Rotas, status HTTP e formato de respostas |
| [09 — Estrutura da Solution](Docs/09-Solution-Structure.md) | Projetos e referências |
| [10 — Padrões de Código](Docs/10-Coding-Standards.md) | Convenções cobradas no code review |
| [11 — Estratégia de Testes](Docs/11-Testing-Strategy.md) | Pirâmide e ferramentas |
| [12 — Ambientes e Deploy](Docs/12-Deployment.md) | Ambiente local, CI e produção |
| [Backlog](Docs/Backlog.md) | Próximas tarefas |

## Rodando localmente

_Será documentado ao final da Sessão 1 do [Backlog](Docs/Backlog.md)._
