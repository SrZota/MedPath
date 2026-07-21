# Roadmap

## Objetivo

Este documento descreve o planejamento evolutivo do MedPath, organizando a implementação em fases incrementais.

Cada etapa representa um conjunto de funcionalidades que agregam valor ao produto, permitindo entregas contínuas e facilitando o acompanhamento do desenvolvimento.

As fases apresentadas poderão ser revisadas conforme a evolução do projeto.

---

# Visão Geral

```text
Planejamento

Fase 0 → Fundação Técnica
Fase 1 → MVP
Fase 2 → Evolução do Produto
Fase 3 → Experiência do Usuário
Fase 4 → Escalabilidade
Fase 5 → Inteligência da Plataforma
```

---

# Fase 0 — Fundação Técnica

## Objetivo

Construir toda a infraestrutura necessária para o desenvolvimento da aplicação.

### Arquitetura

- Estrutura inicial da Solution.
- Modular Monolith.
- Clean Architecture.
- Domain-Driven Design.
- Configuração do Entity Framework Core.
- SQL Server.
- Sistema de Migrations.

### Segurança

- Autenticação JWT.
- Controle de acesso por perfis.

### Qualidade

- Configuração de Logs.
- Tratamento global de exceções.
- Swagger.
- FluentValidation.

---

# Fase 1 — MVP

## Objetivo

Disponibilizar a primeira versão funcional da plataforma.

### Pacientes

- Cadastro.
- Login.
- Recuperação de senha.
- Atualização de perfil.

### Profissionais

- Cadastro.
- Especialidades.
- Perfil profissional.

### Instituições

- Cadastro administrativo.
- Atualização de dados.

### Agendamento

- Visualizar agenda.
- Agendar consulta.
- Cancelar consulta.
- Confirmar consulta.

### Administração

- Cadastro de instituições.
- Gerenciamento de usuários.

---

# Fase 2 — Evolução do Produto

## Objetivo

Adicionar funcionalidades que aumentem o valor da plataforma.

### Avaliações

- Avaliação de consultas.
- Comentários.
- Nota média do profissional.

### Pesquisa

- Busca por especialidade.
- Busca por cidade.
- Busca por bairro.
- Busca por instituição.

### Agenda

- Bloqueio de horários.
- Configuração de disponibilidade.
- Horários recorrentes.

---

# Fase 3 — Experiência do Usuário

## Objetivo

Melhorar a experiência de utilização da plataforma.

### Notificações

- Confirmação de consulta.
- Lembretes.
- Cancelamentos.

### Perfil

- Foto do profissional.
- Currículo.
- Formação.
- Idiomas.

### Dashboard

- Histórico de consultas.
- Próximos atendimentos.
- Indicadores pessoais.

---

# Fase 4 — Escalabilidade

## Objetivo

Preparar a plataforma para crescimento.

### Arquitetura

- Cache.
- Background Services.
- Eventos assíncronos.

### Infraestrutura

- Docker.
- Pipeline CI/CD.
- Monitoramento.
- Observabilidade.

### Performance

- Otimização de consultas.
- Índices adicionais.
- Compressão de respostas.
- Rate Limiting.

---

# Fase 5 — Inteligência da Plataforma

## Objetivo

Adicionar funcionalidades avançadas.

### Inteligência Artificial

- Recomendar profissionais.
- Sugestão de especialidades.
- Busca inteligente.

### Telemedicina

- Chamadas de vídeo.
- Compartilhamento de arquivos.
- Chat.

### Financeiro

- Pagamentos.
- Assinaturas.
- Convênios.

---

# Evolução Técnica

Além das funcionalidades, a arquitetura evoluirá continuamente.

## Etapa 1

- Estrutura da Solution.
- Camadas.
- Modularização.

---

## Etapa 2

- Testes Unitários.
- Testes de Integração.

---

## Etapa 3

- Mensageria.
- Cache.

---

## Etapa 4

- Microsserviços (caso necessário).

---

# Critérios para Avanço de Fase

Uma fase será considerada concluída quando:

- Todas as funcionalidades planejadas estiverem implementadas.
- Todos os testes estiverem aprovados.
- A documentação estiver atualizada.
- As APIs estiverem documentadas no Swagger.
- Não existirem defeitos críticos conhecidos.

---

# Backlog Futuro

As funcionalidades abaixo estão fora do escopo atual, mas poderão ser avaliadas futuramente.

## Produto

- Aplicativo mobile.
- Integração com planos de saúde.
- Telemedicina.
- Assinaturas Premium.
- Marketplace de serviços.

---

## Inteligência Artificial

- Recomendação personalizada de profissionais.
- Assistente virtual.
- Chat inteligente.
- Previsão de disponibilidade.

---

## Integrações

- Google Calendar.
- Outlook Calendar.
- WhatsApp.
- SMS.
- E-mail.

---

# Prioridades

A evolução do MedPath seguirá a seguinte ordem de prioridade:

| Prioridade | Objetivo |
|------------|----------|
| Alta | Estabilizar a arquitetura |
| Alta | Concluir o MVP |
| Média | Melhorar a experiência do usuário |
| Média | Otimizar desempenho |
| Baixa | Funcionalidades avançadas |
| Baixa | Inteligência Artificial |

---

# Indicadores de Evolução

Durante o desenvolvimento, os seguintes indicadores deverão ser acompanhados:

- Funcionalidades concluídas.
- Cobertura de testes.
- Quantidade de bugs.
- Tempo médio de desenvolvimento.
- Tempo médio de resolução de defeitos.
- Percentual da documentação atualizada.

---

# Considerações

O Roadmap representa o planejamento estratégico do MedPath e poderá sofrer ajustes conforme novas necessidades de negócio forem identificadas.

Qualquer alteração relevante deverá ser registrada e revisada antes de sua implementação.