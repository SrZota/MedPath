# Visão do Produto

| Campo | Valor |
|-------|-------|
| Código | VISION-001 |
| Versão | 0.2.0 |
| Status | Aprovado |
| Responsável | PO MedPath |
| Última atualização | 12/10/2026 |

---

## Origem

O MedPath é a evolução do TCC **"Cadê Hospital?"** (ETEC Prof. Horácio Augusto da Silveira, 2018), que propunha localizar a unidade de saúde mais próxima com a especialidade necessária e traçar a rota até ela.

---

## Visão

Ser a forma mais rápida e confiável de responder à pergunta:

> **"Estou precisando de atendimento agora. Para onde eu devo ir?"**

A MedPath é uma plataforma de navegação em saúde que conecta pessoas à unidade de saúde mais adequada, considerando localização, tipo de unidade, especialidades/serviços, convênios aceitos e horário de funcionamento.

---

## Problema

Em um momento de necessidade, principalmente em locais desconhecidos, as pessoas não sabem:

- Qual unidade de saúde está mais próxima;
- Se ela é do tipo certo para o caso (UBS, UPA, pronto-socorro, hospital);
- Se atende a especialidade ou serviço necessário;
- Se aceita seu convênio (ou se é pública);
- Se está aberta agora.

Ferramentas de mapa genéricas mostram "hospitais perto de mim", mas não respondem às perguntas acima. Plataformas de agendamento (ex.: Doctoralia) respondem "com quem marcar consulta daqui a duas semanas", não "para onde ir agora".

---

## Público-alvo

| Grupo | Papel no produto |
|-------|------------------|
| Pacientes / acompanhantes | Usuários principais. Buscam onde ser atendidos. |
| Instituições de saúde | Mantêm seus dados corretos (fase posterior). |
| Administradores MedPath | Curadoria e correção da base de dados. |
| Secretarias de saúde / convênios | Clientes potenciais de dados agregados (futuro). |

---

## Proposta de Valor

- **Para o paciente:** em poucos segundos e sem precisar de cadastro, ver as unidades adequadas mais próximas e abrir a rota.
- **Para as instituições:** ser encontrada pelas pessoas certas e receber feedback do atendimento.
- **Para o poder público:** indicadores de demanda e avaliação de atendimento por região (futuro).

---

## Diferenciais

1. Busca orientada à **necessidade**, não apenas à distância.
2. **Base inicial completa** a partir de dados públicos (CNES/DataSUS) — o produto é útil desde o dia 1, sem depender de adesão de hospitais.
3. **Busca anônima** — ninguém cria conta no meio de uma emergência.

---

## Princípios do Produto

- **Velocidade acima de tudo:** o fluxo principal deve ser concluído em até 3 toques/cliques.
- **Segurança do paciente:** a plataforma **não faz diagnóstico nem triagem clínica**. Toda tela de busca exibe o aviso "Em caso de emergência, ligue 192 (SAMU)".
- **Privacidade:** coletar o mínimo de dados possível (LGPD). Localização é usada na busca e não é armazenada vinculada a pessoas.
- **Confiabilidade dos dados:** toda informação exibe sua origem e data de atualização.

---

## Objetivos do MVP

1. Busca anônima de unidades de saúde próximas à localização do usuário.
2. Filtros por tipo de unidade, especialidade/serviço, convênio e "aberto agora".
3. Página de detalhes da unidade.
4. Rota até a unidade (abrindo o aplicativo de mapas do usuário).
5. Base de dados importada do CNES.
6. Área administrativa autenticada para correção de dados.

---

## Fora do Escopo do MVP

- Agendamento de consultas (planejado para fase posterior).
- Triagem por sintomas / qualquer recomendação clínica.
- Tempo de espera em tempo real.
- Telemedicina e chat.
- Pagamentos e assinaturas.
- Integração com sistemas de convênios e prontuários.
- Aplicativos nativos (o MVP é Web responsivo; a API já atende futuros apps).

---

## Métricas de Sucesso (MVP)

| Métrica | Meta inicial |
|---------|--------------|
| Tempo de resposta da busca (p95) | < 500 ms |
| Buscas que terminam em "abrir rota" | > 40% |
| Unidades com dados confirmados (região piloto) | > 80% |

---

## Glossário

| Termo | Definição |
|-------|-----------|
| Unidade de saúde (Facility) | Estabelecimento físico que presta atendimento: hospital, UPA, UBS, clínica, pronto-socorro. |
| Tipo de unidade | Classificação da unidade (ver Domínio). |
| Especialidade / Serviço | Área de atendimento oferecida pela unidade. |
| Convênio | Plano de saúde aceito pela unidade. "SUS" é tratado como uma opção de atendimento. |
| CNES | Cadastro Nacional de Estabelecimentos de Saúde, base pública do Ministério da Saúde. |
| Região piloto | Área geográfica onde os dados serão validados primeiro (cidade de São Paulo). |
