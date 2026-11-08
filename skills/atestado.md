---
name: atestado
description: Para uso exclusivo do médico responsável. Gera rascunho de atestado no modelo do médico após confirmar os dados e a decisão clínica. O documento exige revisão e assinatura médica antes do uso. Use quando o médico digitar /atestado seguido dos dados do paciente, ou pedir rascunho de atestado ou declaração de afastamento.
---

# Atestado médico

Gera um rascunho de atestado no modelo do médico. Esta skill destina-se exclusivamente ao médico habilitado responsável pela emissão; não deve ser usada diretamente por pacientes ou leigos. O rascunho não é um documento emitido: o médico deve conferir todos os dados e afirmações, aprovar o conteúdo e assiná-lo antes de entregar ou registrar.

## Dados do médico (configurar para cada profissional)

- Nome: [NOME COMPLETO DO MÉDICO]
- CRM: [NÚMERO DO CRM]
- UF do CRM: [UF]
- Cidade e UF padrão: [CIDADE]/[UF]

Cada médico deve substituir esses campos pelos próprios dados profissionais e pelo local de atendimento antes de usar a skill. Não mantenha dados de exemplo de outro profissional. Nunca invente CRM, RQE ou qualquer identificador. Se algum dado estiver ausente, mantenha [PREENCHER] e avise o médico.

## Limites de emissão

- Só declare atendimento, incapacidade para atividades, diagnóstico, período de afastamento ou prescrição quando o médico confirmar esses fatos e a decisão clínica. Não os deduza apenas dos sintomas, do CID ou do pedido do paciente.
- Se faltar confirmação clínica da necessidade de afastamento ou de algum dado essencial, pergunte ao médico antes de redigir a declaração correspondente. Não produza texto que pareça atestar um fato não confirmado.
- Não invente nem infira gênero, diagnóstico, data, horário, local, tratamento ou informação profissional. Use linguagem neutra quando o gênero não tiver sido informado.
- Não inclua o CID no atestado até que o paciente assinale a autorização e a assine. Nunca marque a caixa nem simule a assinatura pelo paciente. Se a autorização ainda não estiver assinada ou o paciente não concordar, omita o código e a linha do CID do atestado.
- O médico deve confirmar que a inclusão do CID está de acordo com a vontade do paciente e com as normas vigentes aplicáveis. Se houver dúvida, sinalize para decisão do médico; não apresente orientação jurídica como certeza.
- Revise o rascunho antes do uso. Nunca afirme que o documento já está assinado, seja digitalmente ou à mão.

## Entrada

Formato livre, por exemplo:

`/atestado Maria Souza, nascimento 14/02/1990, consulta às 11:05 de 27/03/2026, médico confirma afastamento por 3 dias a partir de 27/03/2026, CID J01 informado pelo médico, medicação específica indicada pelo médico`

Extraia:

1. Nome completo da paciente (ou do paciente).
2. Data de nascimento (dd/mm/aaaa).
3. Data e horário da consulta.
4. CID-10: use o código informado pelo médico. Se o médico informar o diagnóstico por nome e solicitar o código, faça a busca descrita na seção "Busca de CID-10" abaixo. Não deduza diagnóstico a partir de sintomas.
5. Número de dias de afastamento e data de início. Se a data de início não vier, use a data da consulta e avise.
6. Consentimento para divulgar o CID-10: verifique se a autorização do paciente está assinada. Se ainda não estiver, deixe a caixa e assinatura em branco e não inclua o código no atestado.
7. Orientação adicional: marque "medicação específica" somente se o médico confirmar que essa orientação foi prescrita ou indicada. Se não for mencionado ou confirmado, deixe em branco.

Se faltar nome, data de nascimento, data e horário do atendimento, duração do afastamento, confirmação da decisão clínica ou outro dado essencial ao texto, pergunte antes de gerar. Se a data de início não vier, use a data da consulta apenas conforme a regra abaixo e destaque isso na conferência para validação do médico.

## Busca de CID-10

Use esta busca somente quando a doença vier por nome e não por código.

1. Pesquise no web_search: `CID-10 {nome da doença} site DATASUS` ou `CID-10 {nome da doença} OPAS`. Fontes válidas: DATASUS/CID-10 oficial (datasus.gov.br) e OMS/OPAS (CID-10 em português). Não use blogs, resumos sem fonte ou sites de terceiros como referência final.
2. Abra a página oficial com web_fetch e localize a categoria exata da doença.
3. Escolha o código mais específico da categoria (ex.: J06.9 em vez de J06, quando o quadro não for especificado). Se houver dúvida entre dois códigos, apresente os dois ao médico e não escolha sozinho.
4. Registre na linha de conferência a fonte usada e o código escolhido.
5. Se as ferramentas de busca não estiverem disponíveis ou não encontrar o código com segurança, deixe `CID-10: [PREENCHER]` e avise. Não invente nem complete de memória.

Pesquise somente diagnósticos informados pelo médico, não sintomas usados para inferir uma doença. Se o médico informar vários diagnósticos, pesquise cada um separadamente e apresente os códigos encontrados na ordem informada.

## Cálculo do período

- Dia de início conta como dia 1.
- Data final = início + (dias − 1).
- Exemplo: 3 dias a partir de 27/03/2026 → até 29/03/2026.
- Valide datas no formato DD/MM/AAAA e exija número de dias inteiro e maior que zero. Se houver data inválida ou ambígua, peça esclarecimento.
- Escreva o período com número por extenso e número entre parênteses, usando singular ou plural corretamente: "um (01) dia" ou "três (03) dias".

## Modelo de rascunho

```
RASCUNHO — REVISAR E ASSINAR ANTES DO USO

Paciente: {NOME}
Data de nascimento: {DATA_NASC}

ATESTADO MÉDICO

Declaro, para os devidos fins, que o(a) paciente acima identificado(a) foi atendido(a) em consulta médica às {HORA} do dia {DATA_CONSULTA} e, conforme avaliação e decisão do médico emitente, necessita de {DIAS_EXTENSO} ({DIAS_NUM}) {DIA_OU_DIAS} de afastamento a partir de {DATA_INICIO}, para fins de repouso e recuperação de sua saúde.

AUTORIZAÇÃO PARA DIVULGAÇÃO DO CID-10
[ ] Concordo com a divulgação do CID-10 neste atestado.

Nome do paciente: {NOME}
Assinatura do paciente (ou representante legal, se aplicável): ______________________________
Data: ____/____/________

CID-10: {CID — inserir somente após a autorização estar assinalada e assinada}

ORIENTAÇÕES ADICIONAIS:
( {X_OU_ESPACO} ) Uso de medicação específica conforme orientação/prescrição médica

Local e Data: {CIDADE}/{UF_CIDADE}, {DATA_EMISSAO_EXTENSO}

Médico Responsável: {NOME_MEDICO}
CRM {CRM}/{UF}

Assinatura do médico emitente: ______________________________
```

Regras de preenchimento:

- Use {DIA_OU_DIAS} com concordância correta. Use flexão de gênero apenas quando informada pelo médico; caso contrário, mantenha a forma neutra do modelo.
- A caixa começa desmarcada. O paciente deve assinalá-la e assinar; não reproduza consentimento como concedido sem essa confirmação. Só então preencha {CID}. Se não houver autorização assinada, retire do atestado a linha CID-10 e o código, mantendo a autorização em branco apenas se ela for apresentada ao paciente para decisão.
- Se a autorização for assinada em formulário separado, inclua o CID apenas após o médico confirmar que a autorização está registrada. Se o médico decidir não incluir o CID, remova também a seção de autorização.
- Preencha {DATA_EMISSAO_EXTENSO} com a data atual somente se conhecida com segurança; caso contrário, use [PREENCHER].
- Não inclua afirmação de assinatura digital. O médico deve usar o método de assinatura apropriado e confirmar que o documento foi efetivamente assinado antes de entregá-lo.
- Se o médico exigir outro modelo institucional, use-o apenas após confirmar que as declarações correspondem ao atendimento e à decisão clínica; não restaure afirmações de assinatura ainda não realizada.
- Data de emissão por extenso, com espaço: "27 de março de 2026".

## Alertas que a skill deve mostrar ao médico (fora do texto do atestado)

- Confirme que o texto corresponde ao atendimento e à decisão clínica, e que o médico revisará e assinará o documento antes do uso.
- Se a consulta não tiver sido no dia de emissão, confira se a data da consulta e a data de início estão corretas.
- Confirme que a autorização para divulgação do CID foi assinalada e assinada pelo paciente antes de inserir o código no atestado. Se não houver autorização, omita o CID.
- Verifique a vigência da Resolução CFM sobre atestados antes de emitir em série.

## Formato de resposta

1. O atestado completo, dentro de um bloco de código para copiar.
2. Uma linha de conferência: período calculado (início e fim), campos [PREENCHER] pendentes e alertas aplicáveis.

Nada além disso: sem introdução e sem conclusão.

## Exemplo

**Entrada:** `/atestado Maria Souza, nascimento 14/02/1990, consulta às 11:05 de 27/03/2026, médico confirma afastamento por 3 dias a partir de 27/03/2026, CID J01 informado pelo médico, autorização do CID ainda não assinada, medicação específica indicada pelo médico`

**Saída:**

```
RASCUNHO — REVISAR E ASSINAR ANTES DO USO

Paciente: Maria Souza
Data de nascimento: 14/02/1990

ATESTADO MÉDICO

Declaro, para os devidos fins, que o(a) paciente acima identificado(a) foi atendido(a) em consulta médica às 11:05 do dia 27/03/2026 e, conforme avaliação e decisão do médico emitente, necessita de três (03) dias de afastamento a partir de 27/03/2026, para fins de repouso e recuperação de sua saúde.

AUTORIZAÇÃO PARA DIVULGAÇÃO DO CID-10
[ ] Concordo com a divulgação do CID-10 neste atestado.

Nome do paciente: Maria Souza
Assinatura do paciente (ou representante legal, se aplicável): ______________________________
Data: ____/____/________

ORIENTAÇÕES ADICIONAIS:
( X ) Uso de medicação específica conforme orientação/prescrição médica

Local e Data: [CIDADE]/[UF], 27 de março de 2026

Médico Responsável: [NOME COMPLETO DO MÉDICO]
CRM [NÚMERO]/[UF]

Assinatura do médico emitente: ______________________________
```

**Conferência:** período 27/03/2026 a 29/03/2026 (3 dias, contagem inclusiva). CID omitido do atestado até o paciente assinalar e assinar a autorização. O atestado é um rascunho e requer revisão e assinatura do médico emitente antes do uso.
