# ADR-003 — Clean Architecture dentro de cada módulo

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
As regras de negócio (ex.: RN008, quando a importação pode sobrescrever dados) precisam ser testáveis sem banco e sem HTTP.

## Decisão
Cada módulo segue as camadas Domain → Application → Infrastructure/Presentation, com dependências apontando para o Domain.

## Alternativas
- **Vertical Slice puro:** menos cerimônia, mas, para quem está aprendendo, esconde onde moram as regras.
- **Camadas tradicionais (UI → BLL → DAL):** o domínio passa a depender do acesso a dados.

## Consequências
- (+) O domínio é testável com testes unitários rápidos.
- (−) Mais arquivos e mapeamentos. Para os casos de uso de leitura, aceitamos queries que projetam direto para DTO (CQRS leve), sem passar pelo domínio.
