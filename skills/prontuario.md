---
name: prontuario
description: Use quando o médico digitar /prontuário seguido de dados de um paciente, ou pedir prontuário, evolução clínica ou registro de consulta a partir de transcrição ou anotações. Não use para atestado, receita ou laudo.
---

# Prontuário (rascunho para revisão médica)

## Visão geral

Produz um rascunho de registro de consulta no modelo do médico, para ser lido, editado e assinado por ele. A skill organiza o que foi dito e observado. Não decide conduta e não completa o que não foi registrado. Hipóteses e diferenciais são sugestões para avaliação médica.

## Quando usar

- O médico digita `/prontuário` com dados ou transcrição da consulta.
- O médico pede prontuário, evolução clínica ou registro de consulta.

Não use para atestado (há skill própria), receita ou laudo.

## Dados fixos do médico

- Nome: Dr. Joselino Rodrigues de Souza Junior
- CRM: 27323/BA
- Cidade padrão: Conceição do Jacuípe/BA

Nunca invente CRM ou outro identificador. Se faltar algum dado, mantenha `[PREENCHER]` e avise.

## Fluxo

### Etapa 1: triagem

Analise a entrada e identifique lacunas nestes campos:

1. Paciente: nome completo (ou iniciais/código), data de nascimento, número do prontuário.
2. Histórico: queixa principal, início e evolução, episódios anteriores, fatores de melhora e piora, tratamentos prévios, comorbidades, cirurgias, alergias, medicações em uso, hábitos, antecedentes familiares relevantes.
3. Exame físico: sinais vitais e principais achados.
4. Diagnóstico: hipótese principal, exames realizados ou solicitados.
5. Conduta: medicações com dose e posologia, repouso, exames, encaminhamentos, retorno.
6. Sinais de alarme: se a transcrição sugere algo grave, confirme se foi avaliado.
7. Local, data da consulta e cidade.

Não gere o prontuário nesta etapa.

### Etapa 2: uma rodada de perguntas

Apresente as lacunas em lista curta, agrupada por seção, e pergunte:

"Há mais alguma informação sobre esta consulta que não esteja na transcrição? Pode complementar ou responder só os pontos acima. Se não houver mais nada, responda 'pode gerar'."

Regras:
- Uma única rodada. Não interrogue campo a campo.
- Priorize o que muda conduta ou hipótese: sinais vitais, exame físico, sinais de alarme, alergias, medicações, conduta decidida.
- Lacunas administrativas (número do prontuário, cidade) entram na lista, mas não bloqueiam a geração.
- Não pergunte o que já está claro na entrada.

### Etapa 3: geração

Gere quando o médico complementar ou pedir para gerar. Se ele enviar mais dados sem responder às perguntas, incorpore-os e pergunte de novo apenas o que continuar faltando, uma única vez. Depois disso, gere com o que houver e registre as lacunas em PENDÊNCIAS.

## Regras de conduta documental

- Não invente achados, sintomas, medicações, doses, resultados ou antecedentes. Se não estiver na entrada, escreva "não informado" ou "não investigado nesta consulta".
- Separe três origens: o que o paciente relatou, o que o médico observou e o que é sugestão da IA. Hipóteses e diferenciais sempre aparecem marcados como sugestão.
- Não registre normalidade de sistema que não foi examinado. "Sem alterações" só vale para o que foi verificado.
- Corrija erros óbvios de transcrição (grafia fonética de medicamentos, por exemplo), mas marque trechos incertos com [verificar].
- Não copie a transcrição. Organize nas seções.
- Deixe de fora conversas paralelas, assuntos administrativos e dados de terceiros sem relevância clínica.
- Texto corrido, técnico e direto, no estilo de registro clínico. Sem frases de preenchimento ("é importante ressaltar", "cabe destacar") e sem adjetivos que não acrescentam informação.

## Estrutura de saída

Use esta ordem e estes títulos:

**Dados do Paciente**
Nome completo, data de nascimento (DD/MM/AAAA) e número do prontuário.

**Histórico clínico**
Queixa principal entre aspas, nas palavras do paciente quando possível. Depois a história da doença atual em ordem cronológica: início, evolução, fatores de melhora e piora, sintomas associados, tratamentos feitos e resposta. Use "refere", "nega" e "relata" de forma consistente. Em seguida, antecedentes pessoais (doenças prévias, cirurgias, alergias, medicações com dose e posologia quando informadas, hábitos), familiares e vacinação, somente quando citados. Destaque alergias medicamentosas no início do bloco.

**Exame físico**
Sinais vitais disponíveis (PA, FC, FR, Tax, SpO2, peso, altura, IMC), seguidos dos achados por sistema ou região. Registre só o que foi examinado.

**Diagnóstico**
- Se houver sinal de alarme (dor torácica com irradiação, déficit neurológico focal, rebaixamento do nível de consciência, sangramento importante, sinais de sepse, ideação suicida), abra a seção com **[ALERTA]** e a descrição do achado, antes de qualquer outro item.
- Hipótese principal, com a justificativa baseada nos dados registrados. Marque como "hipótese do médico" se ele a informou; caso contrário, "sugestão da IA".
- CID-10: só o código confirmado pela fonte oficial (ver seção de CID abaixo). Se não houver, `CID-10: [PREENCHER]`.
- Confirmação: exames realizados ou "sem exames complementares até o momento".
- **Diagnósticos diferenciais (sugestão da IA):** de 2 a 4, em ordem de plausibilidade. Para cada um: nome, achados a favor retirados dos dados, achados contra ou ausentes que ajudariam a excluir, e exame ou avaliação que discrimina, quando houver. O CID de cada diferencial segue a mesma regra.

**Plano de tratamento**
Conduta registrada: medicações com nome, dose, via e posologia; repouso; exames; encaminhamentos. Acompanhamento em [prazo] dias. Orientações ao paciente e critérios de retorno ou urgência, quando registrados. Se a conduta não foi informada, escreva "conduta a definir pelo médico". Medicações só aparecem aqui se o médico decidiu prescrevê-las. Nunca sugira medicação como se estivesse prescrita.

**Data do preenchimento**
"Este prontuário foi preenchido em [DD/MM/AAAA]."

**Fechamento**
[Cidade], [Data da consulta ou da assinatura]

________________________
Assinatura do Profissional de Saúde
Dr. Joselino Rodrigues de Souza Junior – CRM 27323/BA

## Busca de CID-10

Faça a busca só quando houver hipótese diagnóstica e o código não tiver sido informado.

1. Pesquise com `web_search`: `CID-10 {diagnóstico} DATASUS` ou `CID-10 {diagnóstico} OPAS`. Fontes aceitas: DATASUS (Ministério da Saúde) e OPAS/OMS.
2. Abra a página oficial com `web_fetch`, somente se o URL apareceu na busca, e escolha o código mais específico da categoria.
3. Se houver dúvida entre dois códigos, mostre os dois ao médico e marque [verificar].
4. Se não houver fonte segura, deixe `[PREENCHER]`. Nunca complete de memória.

## Após o prontuário

Fora do modelo, nesta ordem:

**PENDÊNCIAS PARA REVISÃO**
Campos "não informado", trechos marcados [verificar], CIDs não confirmados e informações que faltam.

**AVISO**
"Rascunho gerado por IA a partir de transcrição de consulta. Requer revisão, correção e assinatura do médico responsável antes de integrar ao prontuário."

## Formato da resposta

- Etapa 1 e 2: texto curto, lista por seção e a pergunta final. Sem o prontuário.
- Etapa 3: Markdown, títulos em negrito na ordem acima, começando direto por "Dados do Paciente". Sem preâmbulo.

## Exemplo de trecho

Entrada: "dor de garganta há três dias, febre ontem, 38,2, nega alergia, usa losartana, examinou orofaringe hiperemiada, amígdalas com placas, linfonodos cervicais dolorosos"

Saída (trecho):

**Histórico clínico**
Queixa principal: "dor de garganta há três dias". Refere febre desde ontem, aferida em 38,2 °C. Nega alergias. Em uso de losartana, dose e posologia não informadas [verificar].

**Exame físico**
Orofaringe com hiperemia e placas amigdalianas. Linfonodos cervicais dolorosos à palpação. Sinais vitais não informados.

## Erros comuns

| Erro | Correção |
|---|---|
| Preencher sinais vitais com valores típicos | Escreva "não informado" |
| Escrever "sem alterações" em sistema não examinado | Registre só o examinado |
| Apresentar hipótese da IA como diagnóstico do médico | Marque "sugestão da IA" |
| Informar CID de memória | Busque na fonte oficial ou deixe `[PREENCHER]` |
| Sugerir medicação como prescrita | Só registre o que o médico decidiu |
| Perguntar campo a campo | Uma rodada, com as lacunas que mudam conduta |
| Gerar o prontuário na Etapa 1 | Só gere após a resposta do médico ou o pedido "pode gerar" |
