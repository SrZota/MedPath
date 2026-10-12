# ADR-011 — Rota por link para o app de mapas

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
O TCC previa integrar o Google Maps para traçar a rota (Rnf_005). As APIs de rotas cobram por requisição.

## Decisão
No MVP, o botão "Como chegar" abre o app de mapas do usuário com um link universal, por exemplo:
`https://www.google.com/maps/dir/?api=1&destination=<lat>,<lng>`
(com variante para Waze). O mapa exibido na página de resultados é uma questão do frontend e será decidido na Fase 1.

## Alternativas
- **Directions API embutida:** custo variável e mais complexidade, sem ganho real. Na hora da necessidade, o usuário prefere o app de navegação que já usa.

## Consequências
- (+) Custo zero e navegação turn-by-turn melhor que qualquer implementação própria.
- (−) O usuário sai do MedPath para navegar. É aceitável, pois esse é o objetivo final do fluxo.
