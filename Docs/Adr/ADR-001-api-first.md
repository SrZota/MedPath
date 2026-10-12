# ADR-001 - API First Architecture

## Status

Aceito

---

## Data

10/07/2026

---

## Contexto

A MedPath possuirá diversos consumidores:

- Painel Administrativo
- Aplicativo Mobile
- Aplicação Web
- Sistemas Hospitalares
- SDK oficial

A MedPath será composta por diferentes aplicações consumidoras da plataforma, incluindo interfaces desenvolvidas pela própria equipe e integrações com sistemas externos. É necessário definir uma estratégia única de acesso às regras de negócio para garantir consistência, manutenibilidade e escalabilidade.

---

## Decisão

Toda funcionalidade da plataforma MedPath deverá ser exposta por meio de APIs versionadas.

Nenhum cliente terá acesso direto ao banco de dados ou às regras de negócio.

O painel administrativo utilizará exatamente as mesmas APIs disponibilizadas para integrações externas.

Toda comunicação deverá ocorrer exclusivamente através da API oficial da plataforma.

---

## Consequências

### Positivas

- Único ponto de entrada.
- Única regra de negócio.
- Documentação centralizada.
- Facilita criação do SDK.
- Facilita criação do aplicativo mobile.
- Permite evolução independente dos clientes consumidores.

### Negativas

- Pequeno aumento de latência devido às chamadas HTTP.
- Necessidade de versionamento da API.
- Maior dependência da disponibilidade da API.

---

## Alternativas Consideradas

### Acesso direto às camadas da aplicação

Descartado por aumentar o acoplamento entre clientes e regras de negócio.

### APIs específicas para cada consumidor

Descartado por duplicar regras de negócio e dificultar a manutenção.