# Banco de Dados

## Objetivo

Este documento descreve a estratégia de persistência adotada pelo MedPath, definindo convenções, modelagem conceitual, relacionamentos e diretrizes para utilização do banco de dados.

Seu objetivo é garantir consistência, escalabilidade e facilitar a manutenção da aplicação.

---

# Tecnologia

A persistência será realizada utilizando:

| Tecnologia | Utilização |
|------------|------------|
| SQL Server | Banco de Dados Relacional |
| Entity Framework Core | ORM |
| Code First | Modelagem |
| Migrations | Versionamento do Banco |

---

# Estratégia de Modelagem

O banco será desenvolvido utilizando a abordagem **Code First**.

As entidades do domínio serão responsáveis por definir o modelo da aplicação, enquanto a camada de infraestrutura será responsável pelo mapeamento utilizando o Entity Framework Core.

Toda alteração estrutural deverá ocorrer através de Migrations.

---

# Convenções

## Chaves Primárias

Todas as tabelas possuirão uma chave primária denominada:

```text
Id
```

O tipo utilizado será:

```text
uniqueidentifier (GUID)
```

Exemplo:

```sql
Id UNIQUEIDENTIFIER NOT NULL
```

---

## Chaves Estrangeiras

As chaves estrangeiras seguirão a convenção:

```text
NomeDaEntidadeId
```

Exemplos

```text
PacienteId

ProfissionalId

InstituicaoId

ConsultaId
```

---

## Nomes das Tabelas

As tabelas utilizarão nomes no singular.

Exemplo:

```text
Paciente

Profissional

Consulta

Instituicao

Agenda
```

---

## Nomes das Colunas

As propriedades utilizarão PascalCase.

Exemplo

```text
Nome

Email

DataNascimento

CreatedAt
```

---

# Auditoria

Todas as entidades persistidas deverão possuir os seguintes campos.

| Campo | Descrição |
|--------|-----------|
| CreatedAt | Data de criação |
| UpdatedAt | Última alteração |
| DeletedAt | Data de exclusão lógica |
| IsDeleted | Indica exclusão lógica |

---

## Exemplo

```text
CreatedAt

UpdatedAt

DeletedAt

IsDeleted
```

---

# Exclusão Lógica (Soft Delete)

O sistema adotará Soft Delete.

Nenhum registro será removido fisicamente do banco.

Ao excluir um registro, deverão ser atualizados:

```text
IsDeleted = true

DeletedAt = data atual
```

As consultas da aplicação deverão ignorar automaticamente registros excluídos.

---

# Relacionamentos

## Paciente

Relacionamentos

```text
Paciente

1:N

Consulta
```

---

## Profissional

Relacionamentos

```text
Profissional

N:N

Instituicao
```

---

## Instituição

Relacionamentos

```text
Instituicao

1:N

Agenda
```

---

## Consulta

Relacionamentos

```text
Consulta

1:1

Avaliacao
```

---

## Especialidade

Relacionamentos

```text
Especialidade

N:N

Profissional
```

---

# Modelo Conceitual

```text
Paciente
    │
    ├──────────────┐
    │              │
    ▼              │
Consulta           │
    ▲              │
    │              │
Profissional       │
    │              │
    ▼              │
Instituicao        │
    │              │
    ▼              │
Agenda             │
                   │
Consulta ─────────► Avaliacao
```

---

# Índices

Os seguintes índices deverão ser criados.

## Usuário

```text
Email (Unique)
```

---

## Profissional

```text
RegistroProfissional (Unique)
```

---

## Instituição

```text
CNPJ (Unique)
```

---

## Consulta

```text
PacienteId

ProfissionalId

DataHora
```

---

## Agenda

```text
ProfissionalId

DataHora
```

---

# Estratégia de Exclusão

Os relacionamentos utilizarão preferencialmente:

```text
Restrict
```

A exclusão em cascata deverá ser utilizada apenas quando fizer sentido para o domínio.

Como regra geral:

- Evitar Cascade Delete.
- Priorizar integridade dos dados.

---

# Migrations

Toda alteração estrutural deverá ser realizada através de Migrations.

Não será permitido alterar diretamente o banco de dados em ambientes de desenvolvimento ou produção.

---

# Seed de Dados

O sistema poderá possuir dados iniciais para facilitar o desenvolvimento.

Exemplos:

- Especialidades médicas.
- Perfis de usuário.
- Administrador padrão (ambiente de desenvolvimento).
- Configurações iniciais.

---

# Performance

As seguintes práticas deverão ser adotadas:

- Índices para colunas frequentemente pesquisadas.
- Consultas paginadas.
- Evitar SELECT *.
- Utilização de AsNoTracking() para consultas somente leitura.
- Carregamento explícito ou projetado quando necessário.

---

# Concorrência

Entidades críticas poderão utilizar controle de concorrência otimista.

Exemplo:

```text
RowVersion
```

Essa estratégia evita sobrescrita de alterações simultâneas.

---

# Convenções para Entity Framework Core

Os mapeamentos deverão ser realizados utilizando Fluent API.

Não serão utilizadas Data Annotations para configuração do banco de dados.

Cada entidade possuirá sua própria classe de configuração.

Exemplo:

```text
PacienteConfiguration

ProfissionalConfiguration

ConsultaConfiguration
```

Todas as configurações deverão ser centralizadas através de:

```csharp
modelBuilder.ApplyConfigurationsFromAssembly(...)
```

---

# Princípios

A camada de persistência deverá seguir os seguintes princípios:

- O domínio não conhece o banco de dados.
- O domínio não conhece o Entity Framework Core.
- Toda configuração de persistência pertence à camada Infrastructure.
- O banco de dados é um detalhe de implementação.

Esses princípios garantem baixo acoplamento entre as regras de negócio e a tecnologia utilizada para persistência.