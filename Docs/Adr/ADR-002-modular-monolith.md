# ADR-002 — Monólito Modular

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
Um desenvolvedor, um produto em validação e um orçamento mínimo. Mesmo assim, é preciso uma base organizada que possa crescer.

## Decisão
Um único processo implantável, com o código dividido em **módulos isolados** (Facilities, Identity, …). Cada módulo tem schema de banco próprio e expõe apenas contratos públicos.

## Alternativas
- **Microsserviços:** custo operacional (deploy, rede, observabilidade, consistência distribuída) desproporcional para um time de uma pessoa.
- **Monólito em camadas sem módulos:** simples no início, mas o acoplamento cresce sem limite e extrair partes depois fica caro.

## Consequências
- (+) Um deploy, uma transação local e depuração simples.
- (+) Os limites entre módulos ficam explícitos e um módulo pode virar serviço no futuro.
- (−) Exige disciplina: nada de acessar o DbContext de outro módulo. Isso será verificado com testes de arquitetura (Fase 1).
