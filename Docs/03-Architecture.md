# Arquitetura

## Objetivo

Este documento descreve a arquitetura adotada pelo MedPath, apresentando a organização da solução, os padrões arquiteturais utilizados, a divisão em módulos e os princípios que norteiam o desenvolvimento da aplicação.

O objetivo é garantir uma base sólida, escalável e de fácil manutenção, permitindo a evolução contínua do sistema.

---

# Visão Geral

O MedPath será composto por uma aplicação Web consumindo uma API REST desenvolvida em .NET.

A API será implementada utilizando uma arquitetura de **Monólito Modular**, organizada segundo os princípios da **Clean Architecture** e do **Domain-Driven Design (DDD)**.

A comunicação entre cliente e servidor será realizada exclusivamente através de contratos HTTP utilizando JSON.

---

# Visão da Arquitetura

```text
┌──────────────────────────────┐
│         Aplicação Web        │
│      (Angular - Futuro)      │
└──────────────┬───────────────┘
               │ HTTP / JSON
               ▼
┌──────────────────────────────┐
│         MedPath API          │
│          ASP.NET Core        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│      Monólito Modular        │
├──────────────────────────────┤
│ Identity                     │
│ Patients                     │
│ Healthcare                   │
│ Scheduling                   │
│ Reviews                      │
│ Administration               │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│         SQL Server           │
└──────────────────────────────┘
```

---

# Princípios Arquiteturais

A arquitetura seguirá os seguintes princípios:

- Separação de responsabilidades;
- Alta coesão;
- Baixo acoplamento;
- Inversão de dependências;
- Encapsulamento das regras de negócio;
- Independência entre módulos;
- Facilidade para testes automatizados;
- Preparação para futura migração para Microsserviços.

---

# Padrões Arquiteturais

Os seguintes padrões serão utilizados:

- Monólito Modular;
- Clean Architecture;
- Domain-Driven Design (DDD);
- SOLID;
- REST;
- API First;
- Repository Pattern (quando aplicável);
- Dependency Injection.

As decisões arquiteturais serão documentadas individualmente na pasta **ADR**.

---

# Organização da Solution

A solução será organizada da seguinte forma:

```text
src/

MedPath.Api/

Modules/

    Identity/

    Patients/

    Healthcare/

    Scheduling/

    Reviews/

    Administration/

Shared/
```

Cada módulo será responsável pelas próprias regras de negócio, casos de uso e persistência.

---

# Organização Interna dos Módulos

Todos os módulos seguirão a mesma estrutura.

```text
Scheduling

├── Application
├── Domain
├── Infrastructure
└── Presentation
```

## Application

Responsável pelos casos de uso da aplicação.

Exemplos:

- Commands
- Queries
- DTOs
- Validators
- Handlers
- Interfaces

---

## Domain

Contém as regras de negócio do módulo.

Exemplos:

- Entidades
- Value Objects
- Eventos de Domínio
- Agregados
- Interfaces de Domínio

O domínio não deverá depender de nenhuma tecnologia externa.

---

## Infrastructure

Responsável pelos detalhes técnicos.

Exemplos:

- Entity Framework Core
- Repositórios
- Migrations
- Integrações externas
- Serviços de infraestrutura

---

## Presentation

Responsável pela exposição da API.

Exemplos:

- Controllers
- Endpoints
- Filters
- Middlewares específicos do módulo

---

# Comunicação Entre Módulos

Cada módulo deverá possuir responsabilidade única.

Sempre que possível, a comunicação ocorrerá através de interfaces e contratos bem definidos.

Um módulo não deverá acessar diretamente as entidades internas de outro módulo.

---

# Camadas da Aplicação

A arquitetura seguirá o fluxo abaixo:

```text
Controller

↓

Application

↓

Domain

↓

Infrastructure

↓

Banco de Dados
```

As regras de negócio deverão permanecer exclusivamente na camada de Domínio.

---

# Persistência

A persistência será realizada utilizando:

- SQL Server;
- Entity Framework Core;
- Code First;
- Migrations.

O acesso ao banco será encapsulado pela camada de infraestrutura.

---

# Autenticação

A autenticação utilizará:

- JWT (JSON Web Token);
- ASP.NET Identity (quando aplicável);
- Controle de acesso baseado em perfis (Roles).

Os perfis previstos são:

- Paciente;
- Profissional;
- Instituição;
- Administrador.

---

# Documentação da API

A API será documentada utilizando OpenAPI (Swagger).

Toda nova funcionalidade deverá possuir documentação correspondente.

---

# Registro de Logs

O sistema deverá possuir registro estruturado de logs para facilitar auditoria e diagnóstico de problemas.

Eventos importantes deverão ser registrados, como:

- Login;
- Agendamentos;
- Cancelamentos;
- Erros;
- Exceções.

---

# Tratamento de Exceções

A aplicação possuirá tratamento global de exceções.

As respostas de erro seguirão um formato padronizado.

Exemplo:

```json
{
  "success": false,
  "message": "Consulta não encontrada.",
  "errors": []
}
```

---

# Estratégia para Evolução

Embora a aplicação seja inicialmente um Monólito Modular, sua arquitetura foi planejada para permitir futura evolução para Microsserviços.

Cada módulo deverá possuir:

- Responsabilidade única;
- Baixo acoplamento;
- Interfaces bem definidas;
- Independência de implementação.

Essa abordagem reduz o impacto caso algum módulo precise ser extraído para um serviço independente.

---

# Tecnologias

As tecnologias inicialmente previstas são:

| Tecnologia | Finalidade |
|------------|------------|
| .NET 8 | Backend |
| ASP.NET Core | API REST |
| Entity Framework Core | Persistência |
| SQL Server | Banco de Dados |
| JWT | Autenticação |
| Swagger | Documentação da API |
| FluentValidation | Validação |
| Serilog | Registro de Logs |
| xUnit | Testes Unitários |

---

# Referências

As decisões técnicas descritas neste documento são complementadas pelos documentos presentes na pasta **ADR**, onde cada escolha arquitetural é registrada juntamente com sua justificativa.