# ADR-012 — Controllers ou Minimal APIs?

- **Status:** 🟡 Proposto (decisão do desenvolvedor)
- **Data:** 12/10/2026
- **Decidir antes de:** Sessão 2

## Contexto
O ASP.NET Core oferece duas formas de declarar endpoints HTTP. Cada módulo precisa registrar os próprios endpoints no host da API.

## Opções

### A) Controllers (MVC)
```csharp
[ApiController]
[Route("api/v1/facilities")]
public sealed class FacilitiesController(INearbyFacilitiesQuery query) : ControllerBase
{
    [HttpGet("nearby")]
    public async Task<ActionResult<IReadOnlyList<NearbyFacilityResponse>>> Nearby([FromQuery] NearbyRequest request, CancellationToken ct)
        => Ok(await query.ExecuteAsync(request, ct));
}
```
- (+) Ainda é o estilo **mais comum nas vagas e em código legado** no Brasil.
- (+) Convenções prontas (filtros, model binding, `[Authorize]` por classe).
- (−) Mais cerimônia. Agrupa ações por "controller", não por caso de uso.
- (−) Num monólito modular, é preciso configurar o `ApplicationPart` de cada módulo.

### B) Minimal APIs
```csharp
public static class FacilitiesEndpoints
{
    public static IEndpointRouteBuilder MapFacilitiesEndpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("api/v1/facilities").WithTags("Facilities");
        group.MapGet("nearby", async ([AsParameters] NearbyRequest request, INearbyFacilitiesQuery query, CancellationToken ct)
            => TypedResults.Ok(await query.ExecuteAsync(request, ct)));
        return app;
    }
}
```
- (+) É a **direção da Microsoft**: os templates novos usam por padrão e é um pouco mais performático.
- (+) Encaixa naturalmente em módulos: cada um expõe um `MapXxxEndpoints()`.
- (+) `TypedResults` geram o OpenAPI corretamente sem atributos.
- (−) Sem disciplina, vira um `Program.cs` gigante. É preciso organizar por arquivos/grupos desde o início.

## Recomendação do PO/Tech Lead
**B (Minimal APIs)**, organizadas em uma classe de endpoints por módulo. Como você vai encontrar controllers no mercado, vale fazer um pequeno spike comparativo: implementar o endpoint de health em controller também, só para estudo, e depois apagar.

## Decisão
_A preencher pelo desenvolvedor, com a justificativa nas suas palavras._
