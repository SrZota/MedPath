# Estratégia de Testes

| Campo | Valor |
|-------|-------|
| Versão | 0.2.0 |
| Status | Aprovado |
| Última atualização | 12/10/2026 |

---

## Pirâmide

```text
          ▲  poucos   Testes de arquitetura (regras de dependência)   — Fase 1
         ▲▲▲          Testes de integração (API + SQL Server real)
      ▲▲▲▲▲▲▲  muitos Testes unitários (domínio e casos de uso)
```

| Tipo | O que testa | Ferramentas | Onde |
|------|-------------|-------------|------|
| Unitário | Entidades, Value Objects, regras (RN), handlers com dependências falsas | xUnit, Shouldly ou FluentAssertions*, NSubstitute | `tests/*.UnitTests` |
| Integração | Endpoint HTTP → banco real, incluindo a consulta geográfica | xUnit, `WebApplicationFactory`, **Testcontainers** (SQL Server em Docker) | `tests/MedPath.Api.IntegrationTests` |
| Arquitetura | "Domain não referencia EF Core", "módulo X não referencia Y" | NetArchTest ou ArchUnitNET | `tests/MedPath.ArchitectureTests` |

\* O FluentAssertions passou a ter licença comercial a partir da versão 8. Prefira **Shouldly** (MIT) ou fixe a versão 7.

> **Por que não usar banco em memória (InMemory/SQLite)?** Ele não suporta `geography`, e a busca geográfica é o coração do produto. O teste precisa rodar contra o mesmo tipo de banco da produção.

---

## Convenções

- Nome do teste: `Metodo_Cenario_ResultadoEsperado`, por exemplo `Create_WithLatitudeOutOfRange_ShouldFail`.
- Estrutura **Arrange / Act / Assert**, separada por linha em branco.
- Um comportamento por teste.
- Toda regra de negócio (RNxxx) tem ao menos um teste com a regra citada no nome ou num comentário.
- Testes de integração são independentes entre si: cada teste cria os próprios dados.

---

## O que é obrigatório em cada PR

- Regra de domínio nova → teste unitário.
- Endpoint novo → teste de integração do caminho feliz e de pelo menos um erro (400 ou 404).
- Bug corrigido → teste que falhava antes da correção.

## Cobertura

Não haverá meta de percentual no início, porque o número estimula testes inúteis. Acompanhamos a cobertura do **Domain**, que deve ficar próxima de 100%.
