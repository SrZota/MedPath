# Banco de Dados

| Campo | Valor |
|-------|-------|
| Código | DB-001 |
| Versão | 0.2.0 |
| Status | Aprovado |
| Última atualização | 12/10/2026 |

---

## Tecnologia

| Item | Escolha |
|------|---------|
| SGBD | SQL Server 2022 (local via Docker) |
| ORM | EF Core (Code First + Migrations) |
| Geografia | `geography` + `Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite` |

Os motivos dessas escolhas estão no [ADR-005](Adr/ADR-005-sqlserver-efcore.md) e no [ADR-006](Adr/ADR-006-geolocation.md).

---

## Convenções

| Item | Convenção | Exemplo |
|------|-----------|---------|
| Idioma | Inglês ([ADR-010](Adr/ADR-010-code-language.md)) | `HealthFacility` |
| Schema | Um por módulo, minúsculo | `facilities`, `identity` |
| Tabela | PascalCase, plural | `facilities.HealthFacilities` |
| Coluna | PascalCase | `CreatedAt` |
| Chave primária | `Id` do tipo `uniqueidentifier` | — |
| Chave estrangeira | `<Entidade>Id` | `SpecialtyId` |
| Tabela de junção | `<Entidade1><Entidade2>` | `FacilitySpecialties` |
| Índice | `IX_<Tabela>_<Colunas>` | `IX_HealthFacilities_CnesCode` |

> **Plural ou singular?** O EF Core usa o nome do `DbSet` (plural) por padrão. Seguir o padrão do framework evita configuração desnecessária.

> **GUIDs e performance:** GUIDs aleatórios fragmentam o índice clusterizado. Use **GUID v7** (`Guid.CreateVersion7()`, disponível desde o .NET 9), que é ordenado por tempo.

---

## Modelo do MVP

```text
schema facilities
┌─────────────────────────────┐        ┌──────────────────────┐
│ HealthFacilities            │        │ Specialties          │
├─────────────────────────────┤        ├──────────────────────┤
│ Id              PK          │   ┌───►│ Id          PK       │
│ CnesCode        char(7) UQ? │   │    │ Name        nvarchar │
│ Name            nvarchar200 │   │    │ Slug        UQ       │
│ Type            tinyint     │   │    └──────────────────────┘
│ Street, Number, District,   │   │
│ City, State, ZipCode        │   │    ┌──────────────────────┐
│ Location        geography   │   │    │ FacilitySpecialties  │
│ Phone           varchar(11) │   │    ├──────────────────────┤
│ OpeningHours    nvarchar(max) JSON │ │ FacilityId  PK, FK   │
│ Source          tinyint     │◄──┼────│ SpecialtyId PK, FK   │
│ IsActive        bit         │   │    └──────────────────────┘
│ LastVerifiedAt  datetimeoffset │
│ CreatedAt, UpdatedAt        │   │    ┌──────────────────────┐
│ RowVersion      rowversion  │   │    │ HealthPlans          │
└─────────────────────────────┘   │    │ Id, Name, Slug UQ    │
                                  │    └──────────────────────┘
                                  │    ┌──────────────────────┐
                                  └────│ FacilityHealthPlans  │
                                       │ FacilityId, PlanId   │
                                       └──────────────────────┘
schema identity
┌─────────────────────────────┐
│ Users                       │
│ Id, Email UQ, PasswordHash, │
│ Role, IsActive, CreatedAt…  │
└─────────────────────────────┘
```

- `Address` e `GeoLocation` são Value Objects persistidos como **owned types / complex types**, ou seja, colunas na própria tabela.
- `OpeningHours` é persistido como JSON (EF Core mapeia nativamente com `ToJson()`).

---

## Índices obrigatórios

| Tabela | Índice | Motivo |
|--------|--------|--------|
| `HealthFacilities` | **Spatial index** em `Location` | Busca por proximidade (RNF001). |
| `HealthFacilities` | `CnesCode` único filtrado (`WHERE CnesCode IS NOT NULL`) | Importação idempotente (RF012). |
| `HealthFacilities` | `(IsActive, Type)` | Filtros da busca. |
| `Specialties`, `HealthPlans` | `Slug` único | Filtro via query string. |
| `Users` | `Email` único | Login. |

---

## Auditoria e exclusão

- Toda tabela tem `CreatedAt` e `UpdatedAt` (`datetimeoffset`, UTC), preenchidos automaticamente por um **SaveChanges interceptor**, e não manualmente em cada caso de uso.
- **Não existe soft delete genérico.** Cada caso tem a sua regra:
  - Unidades usam `IsActive` (é uma regra de negócio, RN006).
  - Usuários que pedem exclusão (LGPD, RF018) são **removidos de fato** ou anonimizados. Um soft delete genérico manteria dados pessoais que a lei manda apagar.
- Entidades editadas por humanos (`HealthFacility`) usam `RowVersion` para concorrência otimista.

---

## Exclusão em cascata

- Padrão: `Restrict`.
- Exceção: as tabelas de junção (`FacilitySpecialties`, `FacilityHealthPlans`) usam cascade a partir da unidade.

---

## Migrations

- Cada módulo tem a sua pasta de migrations e o seu `__EFMigrationsHistory` no próprio schema.
- Comando padrão (exemplo):
  `dotnet ef migrations add <Nome> -p src/Modules/Facilities/... -s src/MedPath.Api -c FacilitiesDbContext`
- Nenhuma alteração manual de schema em nenhum ambiente.
- Toda migration é revisada no PR, inclusive o SQL gerado (`dotnet ef migrations script`).

---

## Seeds

| Dado | Como |
|------|------|
| Especialidades e convênios básicos (incluindo "SUS") | `HasData` na configuração do EF |
| Administrador de desenvolvimento | Seed executado apenas em `Development`, com senha vinda de user-secrets |
| Unidades de saúde | **Importador CNES** (RF011), nunca via `HasData` |

---

## Boas práticas de consulta

- Queries de leitura usam `AsNoTracking()` e projeção (`Select`) direto para DTO.
- Toda listagem é paginada.
- A distância é calculada **no banco** (`Location.Distance(origem)`), nunca carregando todas as unidades para a memória.
- **Atenção à ordem das coordenadas:** em NetTopologySuite, `new Point(x, y)` significa `x = longitude` e `y = latitude`. Inverter os dois é o bug mais comum nesse tipo de sistema.
