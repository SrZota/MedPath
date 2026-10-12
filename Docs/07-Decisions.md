# Registro de Decisões Arquiteturais

Índice de todos os ADRs (*Architecture Decision Records*). Cada arquivo em [Adr/](Adr/) registra uma decisão com o contexto, as alternativas consideradas e as consequências.

## Como funciona

- Um ADR **aceito nunca é editado** para mudar a decisão. Se a decisão mudar, cria-se um novo ADR que o *substitui*, e o antigo recebe o status "Substituído por ADR-XXX". Assim o histórico do raciocínio fica preservado.
- ADRs **🟡 Propostos** aguardam a decisão do desenvolvedor. Neles, o PO/Tech Lead apresenta as opções e uma recomendação, mas quem decide é quem vai implementar.
- Template: copie qualquer ADR existente (Contexto → Opções/Alternativas → Decisão → Consequências).

## Índice

| ADR | Título | Status |
|-----|--------|--------|
| [001](Adr/ADR-001-api-first.md) | API First | ✅ Aceito |
| [002](Adr/ADR-002-modular-monolith.md) | Monólito Modular | ✅ Aceito |
| [003](Adr/ADR-003-clean-architecture.md) | Clean Architecture por módulo | ✅ Aceito |
| [004](Adr/ADR-004-dotnet-10.md) | .NET 10 LTS | ✅ Aceito |
| [005](Adr/ADR-005-sqlserver-efcore.md) | SQL Server + EF Core | ✅ Aceito |
| [006](Adr/ADR-006-geolocation.md) | Geolocalização com `geography` | ✅ Aceito |
| [007](Adr/ADR-007-cnes-data-source.md) | Base inicial a partir do CNES | ✅ Aceito |
| [008](Adr/ADR-008-authentication-jwt.md) | Autenticação JWT | ✅ Aceito |
| [009](Adr/ADR-009-problem-details.md) | Erros com ProblemDetails | ✅ Aceito |
| [010](Adr/ADR-010-code-language.md) | Código em inglês | ✅ Aceito |
| [011](Adr/ADR-011-route-deep-link.md) | Rota por link para app de mapas | ✅ Aceito |
| [012](Adr/ADR-012-endpoints-style.md) | Controllers ou Minimal APIs | 🟡 Proposto |
| [013](Adr/ADR-013-project-granularity.md) | Projetos por módulo | 🟡 Proposto |
| [014](Adr/ADR-014-use-case-dispatch.md) | Despacho de casos de uso (MediatR?) | 🟡 Proposto |

## Próximos ADRs previstos

| Tema | Quando |
|------|--------|
| Tecnologia do cliente Web (Blazor, Angular, React…) | Início da Fase 1 |
| Provedor de nuvem e hospedagem | Fim da Fase 1 |
| Geocodificação de endereço/CEP | Fase 2 |
