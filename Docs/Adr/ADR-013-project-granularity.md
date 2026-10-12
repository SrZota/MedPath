# ADR-013 — Quantos projetos (.csproj) por módulo?

- **Status:** 🟡 Proposto (decisão do desenvolvedor)
- **Data:** 12/10/2026
- **Decidir antes de:** Sessão 2

## Contexto
A Clean Architecture define camadas (ADR-003) e o monólito modular define módulos (ADR-002). Falta decidir como isso vira projetos no Visual Studio. Essa é uma das decisões mais discutidas em .NET.

## Opções

### A) Um projeto por camada, por módulo
```text
src/Modules/Facilities/
  MedPath.Modules.Facilities.Domain
  MedPath.Modules.Facilities.Application
  MedPath.Modules.Facilities.Infrastructure
  MedPath.Modules.Facilities.Presentation
```
- (+) **O compilador impõe a regra de dependência**: se o Domain tentar usar EF Core, não compila. É ótimo para aprender a regra.
- (+) É a estrutura dos repositórios de referência mais conhecidos de monólito modular em .NET.
- (−) Muitos projetos: 4 por módulo, mais o host e os de teste. Build um pouco mais lento e mais referências para gerenciar.

### B) Um projeto por módulo, camadas como pastas
```text
src/Modules/Facilities/MedPath.Modules.Facilities/
  Domain/  Application/  Infrastructure/  Presentation/
```
- (+) Simples, poucos projetos, e quase tudo pode ser `internal` (encapsulamento forte entre módulos).
- (−) Nada impede o Domain de usar EF Core, exceto disciplina e **testes de arquitetura** (ex.: NetArchTest).

### C) Híbrido: projeto do módulo + projeto de contratos
```text
MedPath.Modules.Facilities            (tudo internal)
MedPath.Modules.Facilities.Contracts  (só o que outros módulos podem usar)
```
- (+) Expõe explicitamente a fronteira pública do módulo.
- (−) Mesma fraqueza da opção B em relação às camadas internas.

## Recomendação do PO/Tech Lead
**A**, porque o objetivo do projeto é **aprender** arquitetura, e ver o compilador recusar uma dependência errada ensina mais que qualquer explicação. O projeto `Contracts` (ideia da opção C) será adicionado quando o primeiro módulo precisar falar com outro (F2).

## Decisão
_A preencher pelo desenvolvedor, com a justificativa nas suas palavras._
