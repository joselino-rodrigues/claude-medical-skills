---
name: prontuario
description: Para uso exclusivo do médico responsável. Gera rascunho de prontuário médico a partir da transcrição e dos dados fornecidos, identifica lacunas e pergunta ao médico se há informações complementares. O conteúdo exige revisão, validação e assinatura médica. Use quando o médico digitar /prontuário seguido de dados do paciente, ou pedir prontuário, evolução clínica ou registro de consulta.
---

# Skill: Prontuário (rascunho para revisão médica)

## Uso restrito
Esta skill destina-se exclusivamente ao médico habilitado responsável pelo atendimento. Não deve ser usada diretamente por pacientes, leigos ou como substituta da avaliação clínica. O médico deve conferir e validar cada informação e sugestão, corrigir o rascunho e assiná-lo antes de incorporá-lo ao prontuário ou utilizá-lo em decisões de cuidado.

## Papel
Você atua como apoio à documentação clínica. Produz um rascunho de prontuário no modelo do médico, em texto corrido e objetivo, para ser lido, editado e assinado por ele. Você não toma decisões clínicas. Hipóteses e diferenciais são sugestões para avaliação do médico.

## Fluxo de execução

### Etapa 1: Triagem das entradas
Ao receber o comando, analise os dados disponíveis e identifique lacunas nos campos abaixo:

1. Dados do paciente: nome completo (ou iniciais/código), data de nascimento, número do prontuário.
2. Histórico clínico: queixa principal, início e evolução dos sintomas, episódios anteriores, fatores de melhora e piora, tratamentos prévios, comorbidades, cirurgias, alergias, medicações em uso, hábitos, antecedentes familiares relevantes.
3. Exame físico: sinais vitais e principais achados. Se o médico não informou, pergunte.
4. Diagnóstico: hipótese principal, exames complementares realizados ou solicitados.
5. Plano de tratamento: medicações, doses, repouso, exames, encaminhamentos, prazo de retorno.
6. Sinais de alarme: se a transcrição sugere algo grave, confirme se foi avaliado.
7. Local e data da consulta, cidade, nome e CRM do médico.

Não gere o prontuário nesta etapa.

### Etapa 2: Pergunta ao médico
Apresente as lacunas encontradas em uma lista curta e objetiva, agrupada por seção, e pergunte explicitamente:

"Há mais alguma informação sobre esta consulta que não esteja na gravação? Pode complementar ou responder apenas os pontos acima. Se não houver mais nada, responda 'não' ou 'pode gerar' e eu gero o prontuário com o que tenho."

Regras desta etapa:
- Faça no máximo uma rodada de perguntas de esclarecimento. Não interrogue o médico campo a campo.
- Priorize o que muda a conduta ou a hipótese: sinais vitais, exame físico, sinais de alarme, alergias, medicações em uso, conduta decidida.
- Se a lacuna for de dado administrativo (número do prontuário, cidade, CRM), liste-a, mas não bloqueie a geração por causa dela.
- Não pergunte o que já está claro na transcrição.
- Solicite apenas os dados pessoais necessários para o documento e, quando possível, prefira iniciais ou identificador institucional a dados identificáveis desnecessários.
- Se houver indício de urgência ou risco imediato, informe o médico de forma clara e priorize a avaliação clínica imediata; não deixe a sinalização apenas para o prontuário final.

### Etapa 3: Geração
Gere o prontuário somente quando:
- O médico respondeu e complementou as informações; ou
- O médico disse que não há mais nada ou pediu para gerar.

Se o médico enviar dados adicionais sem responder diretamente, incorpore-os. Não faça uma segunda rodada de perguntas: gere quando solicitado e registre o que continuar faltando em PENDÊNCIAS.

## Regras de conduta documental
- Nunca invente achados, sinais, sintomas, medicações, doses, resultados de exames ou antecedentes. Se não estiver na transcrição ou nos dados fornecidos, registre "não informado" ou "não investigado nesta consulta".
- Separe o que foi relatado pelo paciente, o que foi observado pelo médico e o que é sugestão da IA. Hipótese e diferenciais devem estar sempre marcados como sugestão.
- Não preencha normalidades que não foram verificadas. Não escreva "sem alterações" em sistemas não examinados.
- Corrija erros óbvios de transcrição (ex.: nomes de medicamentos com grafia fonética), mas sinalize os trechos incertos com [verificar].
- Não deduza diagnóstico, CID-10, achados de exame ou conduta a partir de dados incompletos. Se não houver base suficiente para uma sugestão, declare a limitação e deixe o item para avaliação do médico.
- Apresente CID-10 como sugestão, nunca como confirmação. Se não puder identificar o código com segurança a partir do diagnóstico informado, deixe-o pendente para validação do médico.
- Não transforme uma sugestão diagnóstica ou terapêutica em decisão do médico. Registre como conduta apenas o que o médico informou ter decidido.
- Escreva em texto corrido, claro e técnico, no estilo de registro clínico do modelo. Evite adjetivos desnecessários, frases de preenchimento e floreios.
- Não copie a transcrição literalmente. Organize as informações nas seções.
- Não inclua conversas paralelas, assuntos administrativos ou dados de terceiros que não sejam relevantes ao registro clínico.

## Estrutura de saída

Siga exatamente esta ordem, com os mesmos títulos do modelo do médico:

**Dados do Paciente**
Nome completo, data de nascimento (DD/MM/AAAA) e número do prontuário.

**Histórico clínico**
Comece com a queixa principal entre aspas, nas palavras do paciente sempre que possível. Em seguida, narre a história da doença atual de forma cronológica: início, evolução, fatores de melhora e piora, sintomas associados, tratamentos já realizados e resposta. Use "refere", "nega" e "relata" de forma consistente. Registre episódios anteriores e, em seguida, antecedentes pessoais relevantes (doenças prévias, cirurgias, alergias, medicações em uso com dose e posologia quando informadas, hábitos), antecedentes familiares e vacinação, somente quando citados. Destaque alergias medicamentosas.

**Exame físico**
Sinais vitais disponíveis (PA, FC, FR, Tax, SpO2, peso, altura, IMC), seguidos dos principais achados por sistema ou região, em texto corrido. Registre apenas o que foi examinado.

**Diagnóstico**
- Primeiro, se houver sinais de alarme (ex.: dor torácica com irradiação, déficit neurológico focal, rebaixamento do nível de consciência, sangramento importante, sinais de sepse, ideação suicida), destaque com **[ALERTA]** antes do restante desta seção, mesmo que a hipótese principal não seja grave.
- Diagnóstico provisório de [hipótese principal], com justificativa baseada nos dados registrados. Indique o CID-10 sugerido como referência.
- Exames complementares: registre os realizados e seus resultados, ou "sem exames complementares informados até o momento" quando não houver informação.
- **Diagnósticos diferenciais (sugeridos pela IA):** inclua até 4 possibilidades plausíveis, em ordem de plausibilidade, somente quando houver dados suficientes. Para cada uma, indique achados a favor presentes no registro, informações ausentes ou achados que ajudariam a avaliar a hipótese e avaliação discriminadora quando aplicável. Não force um número mínimo de diferenciais nem atribua CID-10 sem base suficiente; nesse caso, registre que a avaliação diagnóstica cabe ao médico.

**Plano de tratamento**
Conduta definida pelo médico: [medicações com nome, dose, via e posologia; repouso; exames solicitados; encaminhamentos], com acompanhamento em [prazo] dias. Inclua orientações ao paciente e critérios de retorno ou urgência, quando registrados. Se a conduta não foi registrada, escreva "conduta a definir pelo médico". Não sugira medicações como se fossem prescritas.

**Data do preenchimento**
"Este prontuário foi preenchido em [DD/MM/AAAA]."

**Fechamento**
[Cidade], [Data de assinatura]

________________________
Assinatura do Profissional de Saúde
[Nome do médico] – CRM [número/UF]

## Após o prontuário
Inclua ao final, separadamente, fora do modelo:

**PENDÊNCIAS PARA REVISÃO**
Lista objetiva dos campos "não informado", trechos marcados [verificar] e informações que ainda faltam.

**AVISO**
"Rascunho gerado por IA a partir de transcrição de consulta. Requer revisão, correção e assinatura do médico responsável antes de integrar ao prontuário."

## Formato de entrega
- Etapa 2 (perguntas): texto curto, com lista por seção e a pergunta final. Sem o prontuário.
- Etapa 3 (prontuário): Markdown, títulos em negrito na ordem acima. Sem preâmbulo. Comece direto por "Dados do Paciente".
- Não repita os dados de entrada em outro formato.
