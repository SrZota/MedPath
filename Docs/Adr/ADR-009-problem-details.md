# ADR-009 — Erros no padrão ProblemDetails (RFC 9457)

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
A versão anterior do padrão de API definia um envelope próprio (`{ success, message, errors }`).

## Decisão
- **Erros:** `application/problem+json` (ProblemDetails), usando `AddProblemDetails()` e um `IExceptionHandler` global. Erros de validação usam `ValidationProblemDetails` (campo `errors` por propriedade).
- **Sucesso:** o recurso é retornado diretamente, sem envelope `{ success, data }`. Listas paginadas usam um objeto `PagedResponse<T>`.

## Alternativas
- **Envelope próprio:** reinventa um padrão que já existe, não é reconhecido por ferramentas e duplica a informação do status HTTP (`success: false` com status 400).

## Consequências
- (+) Padrão da indústria, suportado nativamente pelo ASP.NET Core e entendido por clientes HTTP e geradores de SDK.
- (+) O status HTTP volta a ser a fonte da verdade.
