# Padrões da API

## Objetivo

Este documento define os padrões utilizados pelas APIs do MedPath.

Seu objetivo é garantir consistência entre os endpoints, facilitar a manutenção do código, melhorar a experiência dos consumidores da API e reduzir ambiguidades durante o desenvolvimento.

Todas as APIs deverão seguir as convenções descritas neste documento.

---

# Princípios

A API do MedPath seguirá os seguintes princípios:

- RESTful
- API First
- Stateless
- Versionada
- Documentada com OpenAPI
- Contratos consistentes
- Respostas padronizadas
- URLs orientadas a recursos

---

# URL Base

Todas as rotas deverão utilizar versionamento.

Exemplo:

```text
/api/v1
```

---

# Estrutura das Rotas

As URLs deverão representar recursos e nunca ações.

## Correto

```text
GET    /api/v1/patients

GET    /api/v1/patients/{id}

POST   /api/v1/patients

PUT    /api/v1/patients/{id}

PATCH  /api/v1/patients/{id}

DELETE /api/v1/patients/{id}
```

---

## Incorreto

```text
/api/GetPatients

/api/CreatePatient

/api/DeletePatient

/api/BuscarPaciente
```

---

# Convenção de Nomes

Os recursos deverão utilizar nomes em inglês.

| Português | API |
|------------|-----|
| Paciente | patients |
| Profissional | professionals |
| Instituição | institutions |
| Consulta | appointments |
| Agenda | schedules |
| Avaliação | reviews |
| Especialidade | specialties |
| Usuário | users |

---

# Métodos HTTP

## GET

Consultar informações.

Nunca altera dados.

---

## POST

Criar recursos.

---

## PUT

Atualizar completamente um recurso.

---

## PATCH

Atualização parcial.

---

## DELETE

Exclusão lógica do recurso.

---

# Status HTTP

## 200 OK

Consulta realizada com sucesso.

---

## 201 Created

Recurso criado.

---

## 204 No Content

Operação concluída sem retorno.

---

## 400 Bad Request

Dados inválidos.

---

## 401 Unauthorized

Usuário não autenticado.

---

## 403 Forbidden

Usuário autenticado, porém sem permissão.

---

## 404 Not Found

Recurso inexistente.

---

## 409 Conflict

Violação de regra de negócio.

Exemplo:

Paciente tentando agendar horário ocupado.

---

## 422 Unprocessable Entity

Validações de domínio.

---

## 500 Internal Server Error

Erro inesperado.

---

# Estrutura das Respostas

## Sucesso

```json
{
  "success": true,
  "data": {}
}
```

---

## Lista

```json
{
  "success": true,
  "data": [
    {}
  ]
}
```

---

## Erro

```json
{
  "success": false,
  "message": "O horário informado não está disponível.",
  "errors": []
}
```

---

# Paginação

Endpoints de listagem deverão suportar paginação.

Exemplo:

```http
GET /api/v1/professionals?page=1&pageSize=20
```

Resposta:

```json
{
  "success": true,
  "page": 1,
  "pageSize": 20,
  "totalItems": 150,
  "totalPages": 8,
  "data": []
}
```

---

# Ordenação

Os endpoints deverão permitir ordenação.

Exemplo:

```text
sort=name

sort=createdAt

sort=rating
```

---

# Filtros

Filtros deverão utilizar Query String.

Exemplo

```http
GET /professionals?city=São Paulo

GET /professionals?specialty=Cardiology

GET /professionals?institution=Hospital Albert Einstein
```

Filtros poderão ser combinados.

---

# Versionamento

Toda API deverá possuir versão.

Formato:

```text
/api/v1

/api/v2
```

Nunca remover versões utilizadas por clientes ativos.

---

# Autenticação

Todas as rotas protegidas utilizarão JWT.

Exemplo:

```http
Authorization: Bearer <token>
```

---

# DTOs

Entidades nunca deverão ser expostas diretamente.

Sempre utilizar DTOs.

Exemplo:

```text
PatientResponse

CreatePatientRequest

UpdatePatientRequest
```

---

# Convenção para Requests

Toda criação deverá utilizar:

```text
Create<Entity>Request
```

Exemplo

```text
CreateAppointmentRequest
```

---

Atualizações:

```text
UpdateAppointmentRequest
```

---

Respostas

```text
AppointmentResponse
```

---

# Commands e Queries

A camada Application utilizará a seguinte convenção.

## Commands

Representam alteração de estado.

Exemplos

```text
CreatePatientCommand

UpdatePatientCommand

DeletePatientCommand

ScheduleAppointmentCommand
```

---

## Queries

Representam consultas.

```text
GetPatientByIdQuery

GetProfessionalsQuery

GetAppointmentsQuery
```

---

# Validação

Todas as validações deverão ocorrer utilizando FluentValidation.

Controllers não deverão possuir validações de negócio.

---

# Erros

Erros deverão possuir mensagens claras.

## Correto

```text
"O horário selecionado já está ocupado."
```

---

## Incorreto

```text
"Erro 154."
```

---

# Idempotência

PUT deverá ser idempotente.

DELETE também.

POST não possui obrigatoriedade de idempotência.

---

# Convenções para Controllers

Nome:

```text
PatientsController

ProfessionalsController

AppointmentsController
```

Nunca:

```text
PatientController
```

---

# OpenAPI

Todos os endpoints deverão possuir:

- Summary
- Description
- Response Codes
- Exemplos

---

# Segurança

Nunca retornar:

- Stack Trace
- Exception.Message
- Dados internos
- Strings de conexão

---

# Boas Práticas

- Utilizar verbos HTTP corretamente.
- Nunca expor entidades.
- Nunca retornar Exception.
- Nunca utilizar SELECT *.
- Nunca implementar regra de negócio em Controllers.
- Utilizar DTOs.
- Padronizar respostas.
- Manter documentação atualizada.

---

# Exemplo Completo

## Endpoint

```http
POST /api/v1/appointments
```

Request

```json
{
  "patientId": "c4d1...",
  "professionalId": "e18f...",
  "scheduleId": "9ac3..."
}
```

Response

```json
{
  "success": true,
  "data": {
    "id": "8c7c...",
    "status": "Scheduled"
  }
}
```

---

# Considerações

Toda nova API desenvolvida para o MedPath deverá seguir as convenções descritas neste documento.

Qualquer alteração de padrão deverá ser registrada através de um ADR e refletida nesta documentação.