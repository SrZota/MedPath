# ADR-004 — .NET 10 (LTS)

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
A documentação anterior previa .NET 8. O suporte do .NET 8 termina em **10/11/2026**. O .NET 10 é a versão LTS atual (lançada em nov/2025, com suporte até nov/2028).

## Decisão
Usar o .NET 10 / C# 14 em todos os projetos, fixando a versão do SDK em um `global.json`.

## Alternativas
- **.NET 8:** perde o suporte antes do MVP ficar pronto.
- **Versão STS (ímpar):** suporte curto demais para um produto.

## Consequências
- (+) Suporte e correções de segurança até 2028.
- (+) Recursos recentes: OpenAPI nativo e `Guid.CreateVersion7()`.
- (−) Parte do material de estudo na internet ainda mostra .NET 6/8. Os conceitos são os mesmos, mas alguns detalhes de template mudam.
