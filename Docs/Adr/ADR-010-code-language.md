# ADR-010 — Código em inglês, documentação em português

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
A documentação anterior misturava tabelas em português (`Paciente`) com rotas em inglês (`/patients`).

## Decisão
- **Inglês:** código, nomes de classes, tabelas, colunas, rotas, mensagens de commit e nomes de branch.
- **Português:** documentação de produto (`Docs/`) e mensagens exibidas ao usuário final.
- A correspondência entre termos fica na Linguagem Ubíqua ([04-Domain](../04-Domain.md)).

## Consequências
- (+) Consistência com o ecossistema .NET e portfólio legível por recrutadores de qualquer país.
- (−) É preciso consultar a tabela de linguagem ubíqua de vez em quando.
