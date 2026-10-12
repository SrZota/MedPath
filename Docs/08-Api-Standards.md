# Padrões da API

| Campo | Valor |
|-------|-------|
| Código | API-001 |
| Versão | 0.2.0 |
| Status | Aprovado |
| Última atualização | 12/10/2026 |

Todas as APIs do MedPath seguem estas convenções. Mudanças de padrão exigem um ADR.

---

## Princípios

RESTful · API First ([ADR-001](Adr/ADR-001-api-first.md)) · stateless · versionada · documentada com OpenAPI · URLs orientadas a recursos · erros em ProblemDetails ([ADR-009](Adr/ADR-009-problem-details.md)).

---

## URLs

- Prefixo de versão: `/api/v1`.
- Recursos em inglês, plural, kebab-case: `/api/v1/facilities`, `/api/v1/health-plans`.
- Endpoints administrativos ficam sob `/api/v1/admin/...` e exigem o perfil `Admin`.
- Nunca usar verbos na URL (`/getFacilities`, `/buscarHospital`). A exceção é uma subcoleção de consulta clara, como `/facilities/nearby`.

### Endpoints do MVP

| Método | Rota | Auth | Requisito |
|--------|------|------|-----------|
| GET | `/api/v1/facilities/nearby?lat=&lng=&radiusKm=&type=&specialty=&healthPlan=&openNow=&page=&pageSize=` | Público | RF001–RF007 |
| GET | `/api/v1/facilities/{id}` | Público | RF008 |
| GET | `/api/v1/specialties` | Público | Filtros |
| GET | `/api/v1/health-plans` | Público | Filtros |
| POST | `/api/v1/auth/token` | Público | RF015 |
| POST | `/api/v1/admin/facilities` | Admin | RF013 |
| PUT | `/api/v1/admin/facilities/{id}` | Admin | RF013 |
| POST | `/api/v1/admin/facilities/{id}/deactivate` | Admin | RF013 |
| POST/PUT | `/api/v1/admin/specialties`, `/api/v1/admin/health-plans` | Admin | RF014 |

> `deactivate` é uma *ação de domínio* modelada como sub-recurso via POST. É mais expressivo que um `PATCH { isActive: false }`, porque dispara uma regra de negócio.

---

## Métodos HTTP

| Método | Uso | Idempotente |
|--------|-----|-------------|
| GET | Consulta. Nunca altera estado. | Sim |
| POST | Cria recurso ou executa ação de domínio. | Não |
| PUT | Substitui o recurso por completo. | Sim |
| PATCH | Atualização parcial (usar só quando necessário). | Não garantido |
| DELETE | Remove o recurso. | Sim |

---

## Status HTTP

| Status | Quando |
|--------|--------|
| 200 OK | Consulta ou atualização com corpo de resposta. |
| 201 Created | Criação. Inclui o header `Location` apontando para o novo recurso. |
| 204 No Content | Sucesso sem corpo. |
| 400 Bad Request | **Entrada inválida**: formato, campo obrigatório, faixa (ex.: `lat=200`). Usa `ValidationProblemDetails`. |
| 401 Unauthorized | Sem token ou token inválido. |
| 403 Forbidden | Autenticado, mas sem permissão. |
| 404 Not Found | Recurso inexistente. |
| 409 Conflict | Violação de regra de negócio ou conflito de estado (ex.: código CNES duplicado, `RowVersion` divergente). |
| 500 Internal Server Error | Erro inesperado. Mensagem genérica, sem detalhes internos. |

> Não usamos 422, para evitar ambiguidade: entrada inválida é **400** e regra de negócio violada é **409**.

---

## Respostas de sucesso

O recurso é retornado diretamente, sem envelope:

```json
{
  "id": "0199d6c2-7a3e-7c41-9a0e-3c2f1b8e4d10",
  "name": "Hospital Municipal Exemplo",
  "type": "Hospital",
  "distanceKm": 1.42,
  "isOpenNow": true
}
```

Listas paginadas:

```json
{
  "items": [ { "...": "..." } ],
  "page": 1,
  "pageSize": 20,
  "totalItems": 57,
  "totalPages": 3
}
```

- Paginação: `page` (padrão 1) e `pageSize` (padrão 20, máximo 100).
- JSON em camelCase, enums como **string** (`"Hospital"`, não `1`).
- Datas em ISO 8601 com fuso (`2026-10-12T14:30:00-03:00`).

---

## Respostas de erro (ProblemDetails)

```json
{
  "type": "https://httpstatuses.io/400",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "lat": ["Latitude deve estar entre -90 e 90."]
  },
  "traceId": "00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01"
}
```

- Mensagens de validação e de regra de negócio em **português**, porque são exibidas ao usuário.
- Nunca expor stack trace, `Exception.Message`, SQL ou strings de conexão.
- O `traceId` permite encontrar o log correspondente.

---

## Filtros e ordenação

- Filtros via query string, combináveis: `?type=Hospital&specialty=pediatria`.
- Filtros por entidade de referência usam o **slug** (`pediatria`), não o Id.
- Ordenação: `sort=name` ou `sort=-name` (descendente), apenas nos campos documentados.

---

## DTOs e nomes

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| Request de criação | `Create<Entity>Request` | `CreateFacilityRequest` |
| Request de atualização | `Update<Entity>Request` | `UpdateFacilityRequest` |
| Response | `<Entity>Response` / `<Entity>SummaryResponse` | `FacilityResponse` |
| Command | `<Verbo><Entity>Command` | `DeactivateFacilityCommand` |
| Query | `Get<Algo>Query` | `GetNearbyFacilitiesQuery` |

Entidades de domínio **nunca** são serializadas na resposta.

---

## Autenticação

`Authorization: Bearer <token>` em todas as rotas protegidas ([ADR-008](Adr/ADR-008-authentication-jwt.md)).

---

## OpenAPI

Todo endpoint deve declarar: summary, tags (o nome do módulo), possíveis status de resposta e tipos de request/response. O documento fica disponível em `/openapi/v1.json`, com UI apenas em Development.

---

## Versionamento

- Mudanças **compatíveis** (novo campo opcional, novo endpoint) ficam na mesma versão.
- Mudanças **incompatíveis** (remover ou renomear campo, mudar tipo) exigem `/api/v2`.
- Uma versão só é removida após aviso prévio aos consumidores.
