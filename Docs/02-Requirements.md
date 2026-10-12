# Especificação de Requisitos

| Campo | Valor |
|-------|-------|
| Código | REQ-001 |
| Versão | 0.2.0 |
| Status | Aprovado |
| Responsável | PO MedPath |
| Última atualização | 12/10/2026 |

> Coluna **Fase**: `MVP` = Fase 1; `F2`, `F3`… = fases do [Roadmap](06-Roadmap.md).

---

## Objetivo

Definir o que o MedPath deve fazer (requisitos funcionais), como deve se comportar (não funcionais) e quais regras de negócio deve respeitar, alinhado à [Visão](01-Vision.md).

---

## Atores

| Ator | Descrição | Fase |
|------|-----------|------|
| Visitante | Pessoa não autenticada. Pode buscar e ver unidades. | MVP |
| Administrador | Equipe MedPath. Gerencia a base de unidades. | MVP |
| Paciente | Visitante com conta. Pode favoritar e avaliar unidades. | F2 |
| Gestor da Instituição | Representante de uma unidade que mantém seus dados. | F3 |
| Sistema Externo | Sistema hospitalar integrado via API. | F4 |

---

## Requisitos Funcionais

### Módulo Facilities — Busca

| ID | Requisito | Fase |
|----|-----------|------|
| RF001 | O visitante deve conseguir buscar unidades de saúde próximas a uma localização (latitude/longitude), ordenadas por distância. | MVP |
| RF002 | A busca deve aceitar um raio máximo em km (padrão 10 km, máximo 50 km). | MVP |
| RF003 | A busca deve permitir filtrar por tipo de unidade. | MVP |
| RF004 | A busca deve permitir filtrar por especialidade/serviço. | MVP |
| RF005 | A busca deve permitir filtrar por convênio aceito, incluindo "SUS". | MVP |
| RF006 | A busca deve permitir filtrar apenas unidades abertas no momento. | MVP |
| RF007 | Cada resultado deve exibir nome, tipo, distância, endereço e se está aberta agora. | MVP |
| RF008 | O visitante deve conseguir ver os detalhes de uma unidade: endereço, telefone, horário de funcionamento, especialidades, convênios, origem e data de atualização dos dados. | MVP |
| RF009 | O visitante deve conseguir abrir a rota até a unidade no aplicativo de mapas do dispositivo. | MVP |
| RF010 | Quando o visitante não permitir acesso à localização, deve ser possível informar um endereço ou CEP. | F2 |

### Módulo Facilities — Gestão da Base

| ID | Requisito | Fase |
|----|-----------|------|
| RF011 | O sistema deve importar unidades de saúde a partir de arquivos do CNES. | MVP |
| RF012 | A importação deve ser idempotente: reimportar o mesmo arquivo atualiza registros pelo código CNES, sem duplicar. | MVP |
| RF013 | O administrador deve conseguir cadastrar, editar e desativar unidades. | MVP |
| RF014 | O administrador deve conseguir manter as listas de especialidades e convênios. | MVP |

### Módulo Identity

| ID | Requisito | Fase |
|----|-----------|------|
| RF015 | O administrador deve conseguir se autenticar com e-mail e senha. | MVP |
| RF016 | O paciente deve conseguir criar conta com e-mail e senha. | F2 |
| RF017 | O usuário deve conseguir recuperar a senha por e-mail. | F2 |
| RF018 | O paciente deve conseguir excluir sua conta e seus dados (LGPD). | F2 |

### Módulo Engagement

| ID | Requisito | Fase |
|----|-----------|------|
| RF019 | O paciente deve conseguir favoritar unidades. | F2 |
| RF020 | O paciente deve conseguir avaliar uma unidade (nota 1–5 e comentário opcional). | F2 |
| RF021 | O administrador deve conseguir moderar avaliações. | F2 |
| RF022 | Os detalhes da unidade devem exibir a nota média e o número de avaliações. | F2 |

### Módulo Institutions

| ID | Requisito | Fase |
|----|-----------|------|
| RF023 | Um gestor deve conseguir solicitar a administração de uma unidade já existente na base ("reivindicar perfil"). | F3 |
| RF024 | O gestor aprovado deve conseguir atualizar os dados da sua unidade. | F3 |
| RF025 | O gestor deve conseguir informar um tempo médio de espera estimado. | F3 |

### Módulo Scheduling

| ID | Requisito | Fase |
|----|-----------|------|
| RF026 | Agendamento de consultas em unidades que oferecerem essa opção. Será detalhado quando a fase for planejada. | F5 |

---

## Requisitos Não Funcionais

| ID | Categoria | Requisito |
|----|-----------|-----------|
| RNF001 | Desempenho | A busca por proximidade deve responder em até 500 ms (p95) com 50 mil unidades na base. |
| RNF002 | Desempenho | Os demais endpoints devem responder em até 2 s (p95). |
| RNF003 | Disponibilidade | Meta de 99,5% de disponibilidade mensal após o lançamento. |
| RNF004 | Segurança | Toda comunicação via HTTPS. Senhas armazenadas com hash seguro (nunca em texto puro). |
| RNF005 | Segurança | Endpoints administrativos exigem autenticação e o perfil Administrador. |
| RNF006 | Privacidade | Conformidade com a LGPD. A localização usada na busca não é persistida vinculada a um usuário. |
| RNF007 | Usabilidade | A interface deve ser responsiva e utilizável em celulares. |
| RNF008 | Usabilidade | Toda tela de busca deve exibir o aviso "Em caso de emergência, ligue 192". |
| RNF009 | Observabilidade | Logs estruturados com identificador de correlação por requisição. |
| RNF010 | Interoperabilidade | Toda funcionalidade disponível via API REST versionada e documentada (OpenAPI). |
| RNF011 | Manutenibilidade | Módulos com alta coesão e baixo acoplamento, sem acesso direto a dados internos de outro módulo. |
| RNF012 | Qualidade | Regras de domínio cobertas por testes unitários; endpoints cobertos por testes de integração. |

---

## Regras de Negócio

| ID | Regra |
|----|-------|
| RN001 | A busca e a visualização de unidades não exigem autenticação. |
| RN002 | A plataforma não fornece diagnóstico, triagem nem recomendação clínica. |
| RN003 | Toda unidade possui exatamente um tipo de unidade. |
| RN004 | Uma unidade pode oferecer várias especialidades e aceitar vários convênios. |
| RN005 | Uma unidade sem coordenadas geográficas válidas não aparece na busca por proximidade. |
| RN006 | Unidades desativadas não aparecem em buscas, mas seu histórico é mantido. |
| RN007 | Unidades marcadas como "24 horas" são sempre consideradas abertas. |
| RN008 | Dados importados do CNES têm origem "CNES"; dados editados manualmente têm origem "Manual" e não são sobrescritos por uma nova importação. |
| RN009 | Instituições não fazem auto cadastro: toda unidade nasce por importação ou pelo administrador. |
| RN010 | Um paciente pode ter no máximo uma avaliação ativa por unidade; uma nova avaliação substitui a anterior. (F2) |
| RN011 | Toda comunicação de clientes com a plataforma ocorre exclusivamente pela API oficial. |

---

## Rastreabilidade com o TCC

| Requisito do TCC (2018) | Situação no MedPath |
|--------------------------|---------------------|
| Rf_001 Localização dos hospitais | RF001 |
| Rf_002 Busca por voz | Descartado (recurso nativo do dispositivo) |
| Rf_004 Localização, contato e especialidades | RF008 |
| Rf_005 Filtros | RF003–RF006 |
| Rf_006 GPS | RF001 (a localização vem do navegador) |
| Rf_008 Raio em km | RF002 |
| Rf_009 Feedback sobre hospitais | RF020 |
| Rf_011 Tipos de unidade | RF003 |
| Rnf_005 Google Maps | RF009 (link para o app de mapas, ver ADR-011) |
