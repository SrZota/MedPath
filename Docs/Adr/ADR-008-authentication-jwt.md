# ADR-008 — Autenticação com JWT

- **Status:** Aceito (implementação na Fase 1)
- **Data:** 12/10/2026

## Contexto
A API é stateless e será consumida por um cliente Web e, no futuro, por apps e sistemas externos (ADR-001).

## Decisão
Access token JWT de curta duração (15 min), assinado pela própria API. Senhas com hash via `PasswordHasher<T>` do ASP.NET Core Identity (PBKDF2). Refresh token será adicionado quando surgir o login de pacientes (F2).

## Alternativas
- **Cookies de sessão:** simples para Web, mas pior para apps e integrações.
- **Provedor externo (Entra ID, Auth0, Keycloak):** mais seguro e completo, porém esconde o aprendizado e adiciona custo ou infraestrutura. Pode ser reavaliado antes de abrir o cadastro de pacientes.

## Consequências
- (+) Padrão de mercado, fácil de consumir.
- (−) Revogar um token antes de expirar exige mecanismo extra. Mitigado pelo tempo de vida curto.
