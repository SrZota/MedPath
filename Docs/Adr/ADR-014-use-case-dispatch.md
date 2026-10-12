# ADR-014 — Como os endpoints chamam os casos de uso?

- **Status:** 🟡 Proposto (decisão do desenvolvedor)
- **Data:** 12/10/2026
- **Decidir antes de:** Sessão 6

## Contexto
Os casos de uso ficam na camada Application como commands e queries. Os endpoints precisam acioná-los, e comportamentos transversais (validação, log, transação) precisam ser aplicados de forma consistente.

## Opções

### A) MediatR
`await sender.Send(new GetNearbyFacilitiesQuery(...))` e *pipeline behaviors* para validação e log.
- (+) Muito popular, aparece em vagas e tutoriais.
- (−) Desde 2025 tem **licença comercial** (gratuita apenas abaixo de um limite de receita). Para um produto que pretende ser vendido, isso precisa ser avaliado.
- (−) Indireção: no "Go to definition" você cai no `Send`, não no handler.

### B) Handlers próprios, injetados diretamente
```csharp
public interface IQueryHandler<TQuery, TResult>
{
    Task<TResult> HandleAsync(TQuery query, CancellationToken ct);
}
```
O endpoint recebe o `IQueryHandler<...>` por injeção de dependência. Validação e log via decorators (ex.: Scrutor) ou chamados explicitamente.
- (+) Zero dependência e zero licença. Você **entende exatamente** o que um mediator faz por baixo.
- (−) Você escreve 30–50 linhas de infraestrutura que o MediatR já traria.

### C) Sem abstração: serviços de aplicação
`FacilityService.SearchNearbyAsync(...)`
- (+) O mais simples possível.
- (−) Os serviços tendem a virar "classes Deus" com dezenas de métodos.

## Recomendação do PO/Tech Lead
**B**: aprendizado máximo e nenhum risco de licença. Se um dia o boilerplate incomodar, a troca para MediatR ou Wolverine é mecânica.

## Decisão
_A preencher pelo desenvolvedor, com a justificativa nas suas palavras._
