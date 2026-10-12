# Estrutura da Solution

| Campo | Valor |
|-------|-------|
| Versão | 0.2.0 |
| Status | 🟡 Provisório: depende do [ADR-013](Adr/ADR-013-project-granularity.md) |
| Última atualização | 12/10/2026 |

> A estrutura abaixo considera a opção **A** do ADR-013 (um projeto por camada). Se outra opção for escolhida, este documento será atualizado.

---

## Árvore

```text
MedPath/
├── global.json                      ← fixa o SDK .NET 10
├── Directory.Build.props            ← configurações comuns a todos os projetos
├── Directory.Packages.props         ← versões centralizadas dos pacotes NuGet
├── .editorconfig                    ← estilo de código
├── MedPath.slnx                     ← solution (formato XML novo)
├── docker-compose.yml               ← SQL Server local
├── Docs/
├── src/
│   ├── MedPath.Api/                 ← host: Program.cs, DI, middlewares, OpenAPI
│   ├── Shared/
│   │   └── MedPath.Shared.Kernel/   ← base de Entity, ValueObject, Result, interfaces comuns
│   └── Modules/
│       ├── Facilities/
│       │   ├── MedPath.Modules.Facilities.Domain/
│       │   ├── MedPath.Modules.Facilities.Application/
│       │   ├── MedPath.Modules.Facilities.Infrastructure/
│       │   └── MedPath.Modules.Facilities.Presentation/
│       └── Identity/                ← Fase 1
└── tests/
    ├── MedPath.Modules.Facilities.UnitTests/
    ├── MedPath.Api.IntegrationTests/
    └── MedPath.ArchitectureTests/   ← Fase 1
```

---

## Referências permitidas

```text
Api ──► Facilities.Presentation ──► Facilities.Application ──► Facilities.Domain ──► Shared.Kernel
 │                                         ▲
 └────► Facilities.Infrastructure ─────────┘
```

| Projeto | Pode referenciar |
|---------|------------------|
| `*.Domain` | `Shared.Kernel` |
| `*.Application` | `*.Domain` |
| `*.Infrastructure` | `*.Application` (e, transitivamente, o Domain) |
| `*.Presentation` | `*.Application` |
| `MedPath.Api` | `*.Presentation`, `*.Infrastructure` (apenas para registrar DI) |
| Módulo X | **Nunca** projetos internos do módulo Y |

---

## Registro de cada módulo no host

Cada módulo expõe **dois** métodos de extensão, e o `Program.cs` só chama esses métodos:

```csharp
builder.Services.AddFacilitiesModule(builder.Configuration);
// ...
app.MapFacilitiesEndpoints();
```

Assim, adicionar ou remover um módulo é uma mudança de duas linhas no host.

---

## Namespaces

O namespace acompanha a pasta: `MedPath.Modules.Facilities.Domain.Facilities` para `HealthFacility`. Use *file-scoped namespaces* (`namespace X;`).
