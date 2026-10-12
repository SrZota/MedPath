# Backlog

Mantido pelo PO. Cada item cabe em uma sessão de ~2h. A ordem é a ordem de execução.

**Legenda:** ⬜ a fazer · 🔄 em andamento · 🔍 em review · ✅ concluído

---

## Sprint 1 — Fase 0: Walking skeleton

**Meta da sprint:** `GET /api/v1/facilities/nearby` retornando unidades reais de São Paulo, com testes e CI verde.

### S1 — Ambiente e fundação do repositório ⬜

**Por quê:** todo projeto profissional começa com um repositório que qualquer pessoa clona e roda em minutos.

Tarefas:
1. Instalar o .NET 10 SDK e o Docker Desktop. Conferir com `dotnet --info` e `docker version`.
2. Criar `docker-compose.yml` na raiz com o SQL Server 2022 (imagem `mcr.microsoft.com/mssql/server:2022-latest`), porta 1433 e volume nomeado para os dados persistirem. A senha do `sa` vem de um arquivo `.env`, que já está no `.gitignore`. Crie também um `.env.example` sem a senha real.
3. Na raiz: `global.json` (SDK 10), `.editorconfig` (`dotnet new editorconfig`), `Directory.Build.props` (`Nullable`, `ImplicitUsings`, `TreatWarningsAsErrors`) e `Directory.Packages.props` (`ManagePackageVersionsCentrally`).
4. Criar a solution `MedPath.slnx` e o projeto `src/MedPath.Api` (`dotnet new webapi`), adicionando-o à solution.
5. Limpar o template (remover o exemplo *WeatherForecast*) e expor `GET /health` com `AddHealthChecks()` / `MapHealthChecks("/health")`.
6. **Estudo + decisão:** ler o [ADR-012](Adr/ADR-012-endpoints-style.md) e o [ADR-013](Adr/ADR-013-project-granularity.md) e trazer a sua escolha com a justificativa.

**Critérios de aceite:**
- `docker compose up -d` sobe o SQL Server e é possível conectar com um cliente SQL.
- `dotnet build` sem warnings e `dotnet run --project src/MedPath.Api` responde `Healthy` em `/health`.
- Nenhuma senha versionada (conferir com `git diff --staged` antes do commit).
- PR aberto a partir de `chore/repo-foundation`.

**O que aprender:** como o MSBuild herda os `Directory.*.props`, para que serve o `global.json`, o gerenciamento central de pacotes, por que segredos não vão para o Git e o básico de Docker Compose.

---

### S2 — Estrutura de projetos do módulo Facilities ⬜
Aplicar a decisão do ADR-013: criar `Shared.Kernel`, os projetos do módulo e os de teste, com as referências permitidas ([09-Solution-Structure](09-Solution-Structure.md)). Criar os métodos `AddFacilitiesModule()` e `MapFacilitiesEndpoints()` (vazios) e chamá-los no host. Escrever um teste unitário trivial passando.
**Aceite:** o build passa e, se o Domain tentar referenciar o Infrastructure, o build falha (testar isso de propósito e reverter).

### S3 — Value Objects ⬜
`GeoLocation`, `CnesCode`, `Address` e `PhoneNumber` com testes unitários das invariantes ([04-Domain](04-Domain.md)). Decisão a tomar junto: **Result pattern ou exceções de domínio?**
**Aceite:** todos os casos inválidos da tabela de invariantes têm um teste.

### S4 — Agregado HealthFacility ⬜
Entidade com `Create`, `UpdateFromCnes`, `EditManually`, `Deactivate`, o Value Object `OpeningHours` e `IsOpenAt`.
**Aceite:** testes para RN005, RN006, RN007 e RN008, além das regras de domínio 1 a 4.

### S5 — Persistência com EF Core ⬜
`FacilitiesDbContext` (schema `facilities`), configurações com Fluent API, NetTopologySuite, conversão `GeoLocation` ↔ `Point`, índice espacial, primeira migration aplicada no banco do Docker e health check do banco.
**Aceite:** tabelas criadas no schema certo e `/health` reportando o banco.

### S6 — Endpoint de busca por proximidade ⬜
Query handler e endpoint `GET /api/v1/facilities/nearby` (lat, lng, radiusKm, paginação), mais um seed manual de 5 unidades próximas da ETEC para testar. Decidir o [ADR-014](Adr/ADR-014-use-case-dispatch.md).
**Aceite:** resultados ordenados por distância, com a distância conferida manualmente no Google Maps para pelo menos uma unidade.

### S7 — Erros e validação ⬜
ProblemDetails, `IExceptionHandler` global e FluentValidation da query (faixas de lat/lng/raio/pageSize).
**Aceite:** `lat=200` retorna 400 com `errors.lat`; uma exceção forçada retorna 500 sem stack trace.

### S8 — Testes de integração ⬜
`WebApplicationFactory` + Testcontainers (SQL Server), com teste do caminho feliz da busca e da validação.
**Aceite:** `dotnet test` sobe o container, roda e derruba sozinho.

### S9–S10 — Importador CNES ⬜
S9: spike (baixar o arquivo, entender colunas/encoding e mapear o tipo de unidade, documentando os achados no ADR-007). S10: implementar o importador como comando de console ou endpoint admin, com upsert e relatório.
**Aceite:** a cidade de São Paulo importada e a reimportação não duplica nada.

### S11 — CI ⬜
Workflow do GitHub Actions ([12-Deployment](12-Deployment.md)) e branch protection na `master`.
**Aceite:** o PR mostra o check verde.

---

## Próximas sprints (rascunho)

- **Sprint 2 — Fase 1, parte 1:** filtros (tipo, especialidade, convênio, aberto agora), detalhes da unidade, listas de referência.
- **Sprint 3 — Fase 1, parte 2:** Identity (JWT, admin) e endpoints administrativos.
- **Sprint 4 — Fase 1, parte 3:** ADR do frontend e cliente Web responsivo.
- **Sprint 5 — Fase 1, parte 4:** deploy.
