# Ambientes e Deploy

| Campo | Valor |
|-------|-------|
| Versão | 0.2.0 |
| Status | Parcial: o deploy em nuvem será decidido no fim da Fase 1 |
| Última atualização | 12/10/2026 |

---

## Ambientes

| Ambiente | Onde | Quando |
|----------|------|--------|
| Development | Máquina do desenvolvedor + SQL Server em Docker | Fase 0 |
| CI | GitHub Actions (build + testes com Testcontainers) | Fase 0 |
| Production | Nuvem (a decidir em ADR) | Fim da Fase 1 |

---

## Ambiente local

Pré-requisitos:

- .NET 10 SDK
- Docker Desktop (com WSL2 no Windows)
- Git
- IDE: Visual Studio 2026, Rider ou VS Code com C# Dev Kit
- Cliente SQL (opcional): Azure Data Studio, SSMS ou extensão de SQL do VS Code

O SQL Server roda via `docker-compose.yml` na raiz (será criado na Sessão 1). A connection string de desenvolvimento fica em **user-secrets**, nunca no `appsettings.json` versionado.

---

## Configuração e segredos

| Tipo | Development | Production |
|------|-------------|------------|
| Connection string | `dotnet user-secrets` | Variável de ambiente / cofre de segredos |
| Chave de assinatura JWT | `dotnet user-secrets` | Cofre de segredos |
| Demais configurações | `appsettings.Development.json` | Variáveis de ambiente |

---

## CI (GitHub Actions), a implementar na Sessão 10

Em todo push e Pull Request:

1. `dotnet restore`
2. `dotnet build -warnaserror`
3. `dotnet test` (unitários + integração com Testcontainers)
4. `dotnet format --verify-no-changes`

O PR só pode ser mergeado com o CI verde (branch protection na `master`).

---

## Produção (a decidir)

Itens que o ADR de hospedagem precisa responder:

- Hospedagem da API (container): Azure App Service / Container Apps, Fly.io, Railway…
- Banco: Azure SQL (há um tier gratuito limitado) ou reavaliação para PostgreSQL ([ADR-005](Adr/ADR-005-sqlserver-efcore.md)).
- Como aplicar as migrations no deploy (bundle `efbundle` executado no pipeline).
- Domínio, HTTPS e custo mensal estimado.
