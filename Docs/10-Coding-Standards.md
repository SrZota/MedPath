# Padrões de Código

| Campo | Valor |
|-------|-------|
| Versão | 0.2.0 |
| Status | Aprovado (evolui com os code reviews) |
| Última atualização | 12/10/2026 |

Regras que o code review vai cobrar. Sempre que um review encontrar um padrão novo, ele é registrado aqui.

---

## Geral

- Código em **inglês** ([ADR-010](Adr/ADR-010-code-language.md)).
- `Nullable` habilitado e **warnings tratados como erro** (`TreatWarningsAsErrors` no `Directory.Build.props`).
- `.editorconfig` na raiz é a fonte da verdade de formatação. Rodar `dotnet format` antes de abrir o PR.
- File-scoped namespaces, um tipo público por arquivo, arquivo com o mesmo nome do tipo.

## Nomes

| Elemento | Convenção | Exemplo |
|----------|-----------|---------|
| Classe, método, propriedade | PascalCase | `HealthFacility.Deactivate()` |
| Parâmetro, variável local | camelCase | `radiusKm` |
| Campo privado | `_camelCase` | `_specialties` |
| Interface | prefixo `I` | `IFacilityRepository` |
| Método assíncrono | sufixo `Async` | `GetByIdAsync` |
| Constante | PascalCase | `MaxRadiusKm` |

## Domínio

- Entidades **sem setters públicos**: o estado muda por métodos com nome de negócio (`Deactivate()`, não `IsActive = false`).
- Construtores privados mais métodos de fábrica (`Create`) que validam as invariantes.
- Value Objects imutáveis (`record` ou `sealed class` com igualdade por valor).
- Coleções expostas como `IReadOnlyCollection<T>`.
- O domínio não referencia EF Core, ASP.NET nem nenhum pacote de infraestrutura.
- Regra violada → `Result` com erro tipado, ou exceção de domínio. A decisão entre os dois será tomada junto na Sessão 3, e é um ótimo tema de estudo.

## Classes

- Classes `sealed` por padrão. Herança só quando for intencional.
- Dependências via construtor (primary constructors são permitidos).
- `CancellationToken` em todo método assíncrono que faz I/O, propagado até o EF Core.
- Sem `async void`. Sem `.Result` nem `.Wait()`.

## Comentários

- O código deve se explicar sozinho pelos nomes. Comentários explicam o **porquê**, nunca o **o quê**.
- Exceção: referenciar a regra de negócio, por exemplo `// RN008: dados manuais não são sobrescritos pela importação`.

## Git

- Branch por tarefa: `feat/<assunto>`, `fix/<assunto>`, `chore/<assunto>`, `test/<assunto>`.
- Commits pequenos no padrão **Conventional Commits** (`feat(facilities): add GeoLocation value object`).
- `git pull` antes de começar cada sessão. Edite o repositório sempre num único lugar (local **ou** GitHub).
- Tudo entra na `master` via Pull Request com review.
