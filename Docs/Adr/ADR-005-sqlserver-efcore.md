# ADR-005 — SQL Server + EF Core

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
Os dados são relacionais (unidade × especialidades × convênios) e precisamos de busca geográfica eficiente.

## Decisão
SQL Server 2022 com EF Core (Code First, Migrations e Fluent API). Um `DbContext` e um schema por módulo.

## Alternativas
- **PostgreSQL + PostGIS:** tecnicamente excelente para dados geográficos e mais barato em nuvem. Foi descartado agora porque SQL Server é o padrão do mercado .NET que o desenvolvedor quer dominar. Pode ser reavaliado antes do deploy (Fase 1), já que o EF Core abstrai a maior parte da diferença.
- **Dapper puro:** mais controle, porém mais código repetitivo e sem migrations.

## Consequências
- (+) Integração de primeira classe com .NET, `geography` nativo e índice espacial.
- (−) O SQL Server gerenciado em nuvem custa mais que PostgreSQL. Ver [12-Deployment](../12-Deployment.md).
