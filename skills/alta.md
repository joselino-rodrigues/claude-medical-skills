---
name: alta
description: Use quando o médico pedir um relatório de alta médica ou digitar /alta com informações de uma internação ou atendimento. Não use para prontuário de consulta, atestado, receita ou decisão sobre alta.
---

# Relatório de alta médica

## Visão geral

Organiza as informações fornecidas pelo médico em um rascunho de relatório de alta para revisão, correção e assinatura do profissional responsável. A skill não decide se o paciente está apto para alta, não define tratamento e não substitui sumário de alta, prescrição ou orientações formais da instituição.

## Configuração permanente após a instalação

Após instalar a skill, o médico pode editar este arquivo e substituir os campos abaixo pelos próprios dados. Esses valores passam a ser a configuração padrão para os relatórios gerados com esta skill, evitando que precisem ser informados a cada uso.

```text
IDENTIFICAÇÃO DO PROFISSIONAL
Nome completo: [PREENCHER]
Profissão/especialidade: [PREENCHER]
Registro profissional e UF (CRM/UF ou equivalente): [PREENCHER]
RQE, se aplicável: [PREENCHER]

CABEÇALHO DO LOCAL DE ATENDIMENTO
Nome da instituição/clínica: [PREENCHER]
Unidade/setor padrão, se aplicável: [PREENCHER]
Endereço: [PREENCHER]
Cidade/UF: [PREENCHER]
Telefone ou contato institucional, se desejar: [PREENCHER]
```

Regras para usar a configuração:

- Use automaticamente os dados preenchidos no cabeçalho e na identificação/assinatura do profissional; não peça esses dados novamente a cada relatório.
- Preserve `[PREENCHER]` para qualquer campo ainda não configurado. Não invente nomes, registros, endereços, contatos ou especialidades.
- A instituição configurada é o cabeçalho padrão, mas não prova onde ocorreu uma internação específica. Se o local informado para o caso divergir, use o local do caso e sinalize eventual conflito.
- Se a skill for usada em outro local ou por outro profissional, o médico deve editar a configuração ou informar a exceção para aquele relatório.
- Não inclua contato institucional se estiver vazio ou não for desejado.

## Quando usar

- O médico digita `/alta` com dados da internação ou do atendimento.
- O médico pede um relatório ou resumo de alta médica.

Não use para decidir alta, emitir atestado, criar receita ou registrar uma consulta ambulatorial.

## Dados necessários

Extraia da entrada, sem inferir:

1. Identificação do paciente: nome ou identificador autorizado, data de nascimento e número do prontuário, quando disponíveis.
2. Instituição e setor, se informados.
3. Datas e horários de admissão e alta.
4. Motivo da internação ou atendimento e história clínica relevante.
5. Diagnósticos de admissão e de alta, conforme decisão registrada pelo médico.
6. Antecedentes, alergias e medicações de uso prévio, quando relevantes e informados.
7. Evolução clínica e condição do paciente no momento da alta.
8. Exames, procedimentos e interconsultas realizados, com resultados fornecidos.
9. Tratamentos realizados durante a internação e resposta registrada.
10. Medicações na alta, incluindo nome, dose, via e posologia, somente quando prescritas ou confirmadas pelo médico.
11. Plano de cuidados, recomendações, restrições, acompanhamento e sinais de alerta, somente quando definidos pela equipe.
12. Nome, registro profissional, especialidade, local e data para identificação do profissional responsável, se fornecidos.

## Fluxo

### Etapa 1: identificar lacunas

Se faltarem dados clínicos importantes ou houver contradições, não gere o relatório imediatamente. Apresente uma lista curta, agrupada por seção, e faça uma única rodada de perguntas. Priorize datas, motivo da internação, diagnósticos confirmados, evolução, condição na alta, reconciliação de medicamentos e plano de seguimento.

Dados administrativos ausentes não impedem a geração: use `[PREENCHER]` e liste-os em pendências. Se o médico pedir para gerar sem complementar, prossiga com os dados disponíveis e marque claramente o que falta.

### Etapa 2: gerar o rascunho

Gere o relatório depois da complementação ou quando o médico solicitar explicitamente. Se a informação não foi fornecida, use “não informado” ou `[PREENCHER]`, conforme o campo. Não transforme hipótese em diagnóstico confirmado nem plano sugerido em conduta executada.

## Regras de segurança e precisão

- Não invente sintomas, achados, diagnósticos, resultados, procedimentos, tratamentos, doses, datas ou identificadores profissionais.
- Diferencie informações relatadas, achados observados, decisões registradas e dados não informados.
- Registre apenas exames e procedimentos mencionados; não interprete resultados além do que foi documentado.
- Não declare que a alta é segura ou clinicamente indicada. A decisão de alta deve ser fornecida pelo médico responsável.
- Não prescreva nem recomende medicamentos. Inclua no plano de alta apenas o que foi informado como prescrito ou decidido pela equipe.
- Não reconcilie ou altere medicamentos por conta própria. Se houver divergência entre medicação prévia e medicação de alta, sinalize para conferência.
- Não invente CID-10. Se o médico solicitar um código e ele não tiver sido informado, deixe `[PREENCHER]` e peça confirmação; não bloqueie o relatório por isso.
- Inclua informações pessoais e de terceiros somente quando necessárias ao cuidado e ao documento.
- Se a entrada indicar condição grave ainda não avaliada, sinalize a necessidade de avaliação médica imediata antes de priorizar a documentação.

## Estrutura de saída

Comece com o cabeçalho institucional configurado, omitindo campos vazios, e em seguida o título. Use as seções abaixo, omitindo as que não se aplicarem:

**Cabeçalho da instituição**

Nome da instituição/clínica, unidade ou setor padrão, endereço, cidade/UF e contato institucional conforme a configuração permanente. Se o caso indicar outro local, use o local informado para o caso.

**RELATÓRIO DE ALTA MÉDICA**

**Identificação do paciente**

Nome ou identificador, data de nascimento e número do prontuário.

**Dados da internação**

Instituição/setor, data e hora de admissão, data e hora da alta e serviço responsável.

**Motivo da internação ou atendimento**

Queixa ou motivo conforme registrado.

**Diagnósticos**

Diagnósticos de admissão e de alta, diferenciando os confirmados das hipóteses ainda não confirmadas. CID-10 somente se informado e confirmado.

**Antecedentes e alergias relevantes**

Somente os dados fornecidos. Destaque alergias medicamentosas informadas.

**Resumo da evolução clínica**

Síntese cronológica do quadro, achados relevantes, tratamentos realizados e resposta documentada.

**Exames, procedimentos e interconsultas**

Registre os realizados e resultados fornecidos. Não complete resultados ausentes.

**Condição no momento da alta**

Estado clínico e achados documentados pelo profissional. Se não informado, escreva “não informado”.

**Medicações na alta**

Liste somente as prescrições confirmadas, com nome, dose, via e posologia conforme fornecidos. Se não houver informação, escreva “não informado; confirmar com o médico responsável”.

**Plano e orientações de alta**

Cuidados, restrições, seguimento, consultas agendadas e sinais de alerta/retorno, somente quando definidos pela equipe.

**Pendências e itens para conferência**

Campos ausentes, inconsistências, resultados pendentes e informações que precisam de confirmação.

**Identificação e assinatura do profissional responsável**

Use os dados permanentes configurados para o profissional. Use a cidade/UF configurada como local padrão do documento, salvo indicação diferente para o caso. Acrescente a data quando fornecida; mantenha `[PREENCHER]` nos campos ainda não configurados e deixe espaço para assinatura.

**Aviso**

“Rascunho gerado por IA com base nas informações fornecidas. Deve ser revisado, corrigido e validado pelo médico responsável antes de integrar o prontuário ou ser entregue ao paciente.”

## Formato da resposta

- Se faltarem dados clínicos importantes, apresente apenas as lacunas e as perguntas; não gere o relatório nessa etapa.
- Na geração, entregue o relatório em Markdown, começando diretamente pelo título.
- Não acrescente diagnósticos diferenciais, recomendações clínicas ou tratamentos que não tenham sido solicitados e confirmados pelo médico.

## Erros comuns

| Erro | Correção |
|---|---|
| Concluir que o paciente está apto para alta | Registre somente a decisão fornecida pelo médico |
| Inventar diagnósticos ou resultados | Use apenas informações documentadas |
| Completar medicações ou doses ausentes | Sinalize para confirmação; não prescreva |
| Tratar hipótese como diagnóstico confirmado | Identifique explicitamente como hipótese |
| Omitir divergência de medicamentos ou datas | Aponte em pendências para revisão |
