# Modelo de Domínio

## Objetivo

Este documento descreve o modelo de domínio do MedPath utilizando os conceitos de **Domain-Driven Design (DDD)**.

Seu objetivo é representar o negócio da plataforma de forma independente da tecnologia utilizada, definindo a linguagem ubíqua, os contextos delimitados, as entidades, os objetos de valor, os agregados e os eventos de domínio.

---

# Visão Geral do Domínio

O MedPath é uma plataforma que conecta pacientes, profissionais e instituições de saúde, permitindo localizar profissionais, consultar disponibilidade, agendar atendimentos e avaliar consultas realizadas.

O domínio foi dividido em contextos delimitados para manter as responsabilidades bem definidas e reduzir o acoplamento entre diferentes áreas do sistema.

---

# Linguagem Ubíqua

Durante o desenvolvimento do projeto, os seguintes termos deverão possuir significado único em toda a aplicação.

| Termo | Descrição |
|--------|-----------|
| Usuário | Pessoa autenticada na plataforma. |
| Paciente | Usuário que realiza consultas. |
| Profissional | Usuário responsável pelos atendimentos. |
| Instituição | Clínica, hospital ou consultório. |
| Especialidade | Área de atuação de um profissional. |
| Agenda | Conjunto de horários disponíveis para atendimento. |
| Consulta | Atendimento agendado entre paciente e profissional. |
| Avaliação | Opinião registrada pelo paciente após a consulta. |
| Disponibilidade | Horários livres para agendamento. |

---

# Contextos Delimitados (Bounded Contexts)

O domínio do MedPath será dividido nos seguintes contextos.

## Identity

Responsável pelo gerenciamento da identidade dos usuários da plataforma.

### Responsabilidades

- Cadastro de usuários.
- Autenticação.
- Autorização.
- Recuperação de senha.
- Controle de acesso.

---

## Patients

Responsável pelas informações relacionadas aos pacientes.

### Responsabilidades

- Cadastro de pacientes.
- Atualização de dados pessoais.
- Histórico de consultas.
- Informações de contato.

---

## Healthcare

Responsável pelo gerenciamento dos profissionais e instituições.

### Responsabilidades

- Cadastro de profissionais.
- Cadastro de especialidades.
- Gerenciamento das instituições.
- Vínculo entre profissionais e instituições.

---

## Scheduling

Responsável pelo gerenciamento das agendas e consultas.

### Responsabilidades

- Disponibilidade.
- Agendamentos.
- Cancelamentos.
- Confirmações.
- Histórico de consultas.

---

## Reviews

Responsável pelas avaliações realizadas pelos pacientes.

### Responsabilidades

- Registro das avaliações.
- Consulta das avaliações.
- Cálculo da reputação dos profissionais.

---

## Administration

Responsável pelas operações administrativas da plataforma.

### Responsabilidades

- Cadastro de instituições.
- Administração de usuários.
- Moderação de avaliações.
- Configurações da plataforma.

---

# Entidades

As entidades representam objetos que possuem identidade própria dentro do domínio.

## Usuário

Representa uma identidade que possui acesso ao sistema.

### Responsabilidades

- Autenticar.
- Alterar senha.
- Atualizar credenciais.

---

## Paciente

Representa uma pessoa que busca atendimento.

### Responsabilidades

- Atualizar dados pessoais.
- Agendar consultas.
- Cancelar consultas.
- Avaliar atendimentos.

---

## Profissional

Representa um profissional habilitado para realizar atendimentos.

### Responsabilidades

- Gerenciar agenda.
- Informar especialidades.
- Atuar em instituições.
- Realizar atendimentos.

---

## Instituição

Representa um estabelecimento de saúde.

### Responsabilidades

- Gerenciar profissionais.
- Atualizar informações.
- Disponibilizar agendas.

---

## Consulta

Representa um atendimento agendado.

### Responsabilidades

- Agendar.
- Confirmar.
- Cancelar.
- Finalizar.

---

## Agenda

Representa os horários disponíveis de atendimento de um profissional.

### Responsabilidades

- Disponibilizar horários.
- Bloquear horários.
- Liberar horários.

---

## Avaliação

Representa uma avaliação realizada por um paciente.

### Responsabilidades

- Registrar nota.
- Registrar comentário.

---

## Especialidade

Representa uma área de atuação profissional.

Exemplos:

- Cardiologia
- Dermatologia
- Pediatria
- Psicologia
- Nutrição

---

# Value Objects

Objetos de valor representam conceitos do domínio que não possuem identidade própria.

## Email

Representa um endereço eletrônico válido.

---

## Telefone

Representa um número de telefone válido.

---

## Endereço

Representa uma localização física.

---

## Registro Profissional

Representa registros emitidos por conselhos profissionais.

Exemplos:

- CRM
- CRO
- CRP
- CREFITO

---

## Horário

Representa um período disponível para atendimento.

---

# Agregados

Os agregados definem os limites de consistência das regras de negócio.

## Agregado Paciente

Raiz:

- Paciente

Objetos relacionados:

- Endereço

---

## Agregado Profissional

Raiz:

- Profissional

Objetos relacionados:

- Especialidades
- Agenda

---

## Agregado Instituição

Raiz:

- Instituição

Objetos relacionados:

- Profissionais

---

## Agregado Consulta

Raiz:

- Consulta

Objetos relacionados:

- Horário

---

## Agregado Avaliação

Raiz:

- Avaliação

---

# Eventos de Domínio

Os eventos representam acontecimentos relevantes para o negócio.

## ConsultaAgendada

Disparado quando uma consulta é criada com sucesso.

---

## ConsultaCancelada

Disparado quando uma consulta é cancelada.

---

## ConsultaConfirmada

Disparado quando uma consulta é confirmada.

---

## ConsultaFinalizada

Disparado quando uma consulta é concluída.

---

## AvaliacaoRegistrada

Disparado quando um paciente registra uma avaliação.

---

# Regras de Domínio

As principais regras de negócio do domínio são:

- Um paciente deve possuir um usuário válido para acessar a plataforma.
- Um profissional deve possuir um registro profissional válido.
- Instituições não podem realizar auto cadastro.
- Apenas administradores podem cadastrar instituições.
- Um profissional pode atuar em uma ou mais instituições.
- Uma instituição pode possuir diversos profissionais.
- Um paciente pode possuir diversas consultas.
- Um horário não pode ser reservado por mais de uma consulta.
- Apenas consultas finalizadas podem ser avaliadas.
- Uma consulta poderá possuir apenas uma avaliação.

---

# Relacionamentos Conceituais

```text
Paciente
    │
    │ realiza
    ▼
Consulta
    ▲
    │
Profissional
    │
    │ pertence a
    ▼
Instituição

Profissional
    │
    │ possui
    ▼
Especialidade

Profissional
    │
    │ possui
    ▼
Agenda

Consulta
    │
    │ gera
    ▼
Avaliação
```

---

# Considerações

O modelo de domínio deve permanecer independente de qualquer tecnologia de implementação.

Alterações em banco de dados, frameworks, bibliotecas ou infraestrutura não deverão impactar as regras descritas neste documento.

A implementação técnica do domínio será detalhada nos documentos de Arquitetura e Banco de Dados.