# Roadmap

| Campo | Valor |
|-------|-------|
| Código | ROADMAP-001 |
| Versão | 0.2.0 |
| Status | Aprovado |
| Última atualização | 12/10/2026 |

---

## Premissas

- Desenvolvedor solo, com **sessões de ~2h por dia**.
- Cada sessão entrega algo integrado à `master` via Pull Request.
- O detalhamento das tarefas fica no [Backlog](Backlog.md). Este documento mostra apenas o quadro geral.

---

## Visão Geral

```text
Fase 0 → Fundação técnica          (walking skeleton)
Fase 1 → MVP: "Para onde eu vou?"  (busca + admin + web)
Fase 2 → Conta do paciente         (favoritos, avaliações, busca por endereço)
Fase 3 → Instituições              (gestores mantêm os próprios dados, tempo de espera)
Fase 4 → Escala e integrações      (API para sistemas externos, cache, observabilidade)
Fase 5 → Agendamento e inteligência
```

---

## Fase 0: Fundação técnica

**Objetivo:** ter um endpoint de busca por proximidade funcionando de ponta a ponta, com dados reais, testes e CI. É um *walking skeleton*: o esqueleto completo do sistema, ainda magro.

- Ambiente local: .NET 10 SDK, Docker e SQL Server em container.
- Solution com host da API e módulo Facilities.
- Domínio `HealthFacility` com testes unitários.
- EF Core com schema próprio, migrations e coluna `geography`.
- Importador CNES (amostra da cidade de São Paulo).
- `GET /api/v1/facilities/nearby`.
- ProblemDetails, validação, logs e health check.
- Testes de integração com banco real (Testcontainers).
- CI no GitHub Actions.

**Concluída quando:** um `curl` com a latitude/longitude da ETEC Horácio Augusto da Silveira retorna as unidades reais mais próximas, e o CI está verde.

---

## Fase 1: MVP

**Objetivo:** produto utilizável por qualquer pessoa na região piloto (cidade de São Paulo).

- Filtros: tipo, especialidade, convênio, aberto agora.
- Detalhes da unidade e link de rota.
- Módulo Identity: login de administrador com JWT.
- Área admin: CRUD de unidades, especialidades e convênios.
- Importação completa do CNES (estado de SP).
- Cliente Web responsivo. A tecnologia será decidida em ADR no início da fase.
- Primeiro deploy em nuvem.

**Concluída quando:** o fluxo "abrir o site → permitir localização → filtrar → abrir rota" funciona em produção, no celular, em menos de 3 toques.

---

## Fase 2: Conta do paciente

- Cadastro, login e recuperação de senha do paciente.
- Exclusão de conta (LGPD).
- Favoritos.
- Avaliações e moderação (o "feedback" do TCC original).
- Busca por endereço/CEP (geocodificação).

---

## Fase 3: Instituições

- Reivindicação de perfil por gestores.
- Gestor atualiza os dados e o tempo médio de espera estimado.
- Indicadores básicos para a instituição.

---

## Fase 4: Escala e integrações

- API para sistemas externos (chaves de API, rate limiting por cliente).
- Cache da busca.
- Observabilidade (métricas, tracing com OpenTelemetry).
- Expansão para outros estados.

---

## Fase 5: Agendamento e inteligência

- Agendamento em unidades parceiras (o modelo da versão 0.1 do domínio será revisitado).
- Recomendação personalizada.
- Aplicativos nativos.

---

## Critérios de pronto (Definition of Done) de qualquer tarefa

- Código revisado em PR.
- Build e testes passando no CI.
- Testes novos para regras novas.
- Endpoint documentado no OpenAPI.
- Documentação afetada atualizada (responsabilidade do PO).
