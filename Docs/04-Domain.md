# Modelo de Domínio

| Campo | Valor |
|-------|-------|
| Código | DOMAIN-001 |
| Versão | 0.2.0 |
| Status | Aprovado (MVP); fases futuras em esboço |
| Última atualização | 12/10/2026 |

---

## Objetivo

Descrever o negócio do MedPath com os conceitos de **Domain-Driven Design (DDD)**, sem depender de tecnologia. O código deve usar exatamente os nomes da coluna **Termo no código**, o que garante uma linguagem ubíqua entre documento e implementação.

---

## Linguagem Ubíqua

| Termo (negócio) | Termo no código | Significado |
|-----------------|-----------------|-------------|
| Unidade de saúde | `HealthFacility` | Estabelecimento físico que presta atendimento. |
| Código CNES | `CnesCode` | Identificador oficial da unidade no CNES (7 dígitos). |
| Tipo de unidade | `FacilityType` | Hospital, UPA, UBS etc. |
| Especialidade / serviço | `Specialty` | Área de atendimento oferecida. |
| Convênio | `HealthPlan` | Plano de saúde aceito. "SUS" é um registro especial. |
| Localização | `GeoLocation` | Par latitude/longitude. |
| Endereço | `Address` | Endereço postal. |
| Horário de funcionamento | `OpeningHours` | Janelas de atendimento por dia da semana, ou 24h. |
| Origem do dado | `DataSource` | `Cnes` ou `Manual`. |
| Busca por proximidade | `NearbySearch` | Consulta de unidades num raio a partir de um ponto. |

---

## Bounded Contexts

```text
┌─────────────────────────┐      ┌───────────────┐
│       Facilities        │      │   Identity    │
│ catálogo + busca + CNES │      │ usuários/auth │
└───────────▲─────────────┘      └───────▲───────┘
            │ contratos públicos         │
┌───────────┴─────────────┐              │
│   Engagement (F2)       │──────────────┘
│ favoritos, avaliações   │
└─────────────────────────┘
```

O Engagement guarda apenas o `FacilityId` e o `UserId`. Ele não conhece as entidades internas dos outros módulos.

---

## Contexto Facilities (MVP)

### Agregado `HealthFacility` (raiz)

| Atributo | Tipo | Regras |
|----------|------|--------|
| `Id` | Guid | Gerado pelo sistema. |
| `CnesCode` | `CnesCode?` (VO) | Opcional (unidades manuais podem não ter), único quando presente. |
| `Name` | string | Obrigatório, até 200 caracteres. |
| `Type` | `FacilityType` | Obrigatório (RN003). |
| `Address` | `Address` (VO) | Obrigatório. |
| `Location` | `GeoLocation?` (VO) | Sem localização, a unidade não aparece na busca (RN005). |
| `Phone` | `PhoneNumber?` (VO) | Opcional. |
| `OpeningHours` | `OpeningHours` (VO) | Padrão: desconhecido. |
| `Specialties` | coleção de `SpecialtyId` | N:N. |
| `HealthPlans` | coleção de `HealthPlanId` | N:N. |
| `Source` | `DataSource` | `Cnes` ou `Manual` (RN008). |
| `IsActive` | bool | Unidades inativas não aparecem na busca (RN006). |
| `LastVerifiedAt` | DateTimeOffset | Data da última importação ou edição. |

**Comportamentos (métodos da entidade, não setters públicos):**

- `Create(...)`: valida invariantes e cria a unidade.
- `UpdateFromCnes(...)`: atualiza os dados apenas se `Source == Cnes` (RN008).
- `EditManually(...)`: aplica a edição e muda `Source` para `Manual`.
- `Relocate(GeoLocation)`: altera a localização.
- `AddSpecialty / RemoveSpecialty`, `AcceptHealthPlan / RemoveHealthPlan`.
- `Deactivate()` / `Activate()`.
- `IsOpenAt(DateTimeOffset)`: retorna `true` se for 24h (RN007) ou se o horário estiver dentro de alguma janela.

### Entidades de referência

- **`Specialty`**: `Id`, `Name`, `Slug`. Exemplos: Pediatria, Ortopedia, Cardiologia, Obstetrícia, Odontologia.
- **`HealthPlan`**: `Id`, `Name`, `Slug`. "SUS" é um registro criado pelo seed.

### Value Objects

| VO | Invariantes |
|----|-------------|
| `GeoLocation` | Latitude ∈ [-90, 90], longitude ∈ [-180, 180]. Imutável. Igualdade por valor. |
| `CnesCode` | Exatamente 7 dígitos numéricos. |
| `Address` | Logradouro, número (opcional), bairro, cidade, UF (2 letras válidas), CEP (8 dígitos). |
| `PhoneNumber` | Apenas dígitos, 10 ou 11 caracteres (com DDD). |
| `OpeningHours` | `Is24Hours` ou uma lista de janelas `(DayOfWeek, Open, Close)` com `Open < Close`. |

### `FacilityType` (enum)

| Valor | Descrição |
|-------|-----------|
| `Hospital` | Hospital geral ou especializado |
| `EmergencyCare` | UPA / pronto-socorro / pronto-atendimento |
| `PrimaryCare` | UBS / posto de saúde |
| `Clinic` | Clínica / ambulatório especializado |
| `Laboratory` | Laboratório / diagnóstico por imagem |
| `Other` | Demais tipos do CNES |

> A tabela que converte o código de tipo do CNES para esse enum fica no importador (Infrastructure) e não no domínio.

### Regras de domínio

1. Uma unidade com `CnesCode` não pode ter o código alterado.
2. A importação nunca sobrescreve uma unidade com `Source == Manual`.
3. Uma unidade não pode ter a mesma especialidade ou o mesmo convênio repetido.
4. Unidade 24h ignora as janelas de horário.

### Read model: busca por proximidade

A busca é uma **query** e não passa pelo agregado: ela lê direto do banco e projeta para DTOs (CQRS leve).

```text
Entrada: GeoLocation origem, raio (km), filtros opcionais
Saída:   lista de { Id, Name, Type, DistanceKm, Address, IsOpenNow }
Ordem:   distância ascendente
```

---

## Contexto Identity (MVP)

- **`User`**: `Id`, `Email` (VO, único), `PasswordHash`, `Role` (`Admin` | `Patient`), `IsActive`.
- No MVP existe apenas o perfil `Admin`. O primeiro administrador é criado pelo seed de desenvolvimento.

---

## Contexto Engagement (F2, esboço)

- **`Review`**: `Id`, `FacilityId`, `UserId`, `Rating` (1–5), `Comment?`, `Status` (`Published` | `Hidden`). Uma por usuário e unidade (RN010).
- **`Favorite`**: `UserId`, `FacilityId`.
- Evento de domínio `ReviewSubmitted`, que permite recalcular a nota média.

---

## Eventos de domínio (MVP)

| Evento | Quando |
|--------|--------|
| `FacilityCreated` | Uma unidade é criada (manual ou importação). |
| `FacilityDeactivated` | Uma unidade é desativada. |

> No MVP os eventos servem apenas para auditoria e logs. Eles não precisam de mensageria.
