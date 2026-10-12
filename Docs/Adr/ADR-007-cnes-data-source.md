# ADR-007 — Base inicial a partir do CNES

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
Sem dados não existe produto. Depender de cada hospital se cadastrar levaria meses e travaria o lançamento (problema do "ovo e da galinha").

## Decisão
Popular a base com os dados abertos do **CNES (Cadastro Nacional de Estabelecimentos de Saúde)**, publicados pelo Ministério da Saúde (OpenDataSUS / dados.gov.br). O importador:

1. Lê o arquivo CSV de estabelecimentos.
2. Converte o código de tipo de unidade do CNES para o nosso `FacilityType`.
3. Faz *upsert* pelo `CnesCode` (RF012), respeitando a RN008 (não sobrescreve dados manuais).
4. Gera um relatório: inseridos, atualizados, ignorados e inválidos (ex.: sem coordenadas).

## Pontos a validar na sessão de importação
- Layout atual do arquivo (nomes de colunas, encoding, separador).
- Qualidade das coordenadas (há registros sem coordenadas ou com coordenadas erradas).
- **Especialidades e convênios:** o CNES descreve serviços e tipos de atendimento (ex.: SUS), não necessariamente "especialidades" do jeito que o paciente pensa. No MVP, o tipo de unidade e a flag SUS vêm do CNES; especialidades e convênios privados são curados manualmente na região piloto.

## Alternativas
- **Cadastro manual pelo admin:** não escala.
- **Google Places API:** paga, termos de uso restritivos para armazenar dados e sem informação de especialidade.

## Consequências
- (+) Produto útil desde o primeiro dia, com dados oficiais.
- (−) Os dados podem estar desatualizados. Por isso exibimos a origem e a data (Visão, princípios) e, na F3, os gestores podem corrigir.
