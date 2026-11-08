---
name: atestado
description: Use quando o médico digitar /atestado seguido dos dados de um paciente, ou pedir atestado, declaração de afastamento ou repouso informando nome, data de nascimento, consulta e número de dias. Não use para prontuário ou receita.
---

# Atestado médico

## Visão geral

Gera o texto do atestado no modelo fixo do médico, pronto para impressão ou para colar no prontuário. O documento só tem validade depois de revisado e assinado pelo médico emitente. A skill não decide diagnóstico, não escolhe CID sem fonte e não altera a redação do modelo.

## Configuração permanente após a instalação

Após instalar a skill, o médico pode editar este arquivo e substituir os campos abaixo pelos próprios dados. Esses valores serão reutilizados nos atestados, sem que precisem ser informados a cada uso.

```text
IDENTIFICAÇÃO DO PROFISSIONAL
Nome completo: [PREENCHER]
Profissão/especialidade: [PREENCHER]
CRM: [PREENCHER]
UF do CRM: [PREENCHER]
RQE, se aplicável: [PREENCHER]

CABEÇALHO DO LOCAL DE ATENDIMENTO
Nome da instituição/clínica: [PREENCHER]
Unidade/setor padrão, se aplicável: [PREENCHER]
Endereço: [PREENCHER]
Cidade: [PREENCHER]
UF: [PREENCHER]
Telefone ou contato institucional, se desejar: [PREENCHER]
```

Regras para usar a configuração:

- Use automaticamente os dados preenchidos no cabeçalho, no local e na identificação do médico; não peça esses dados novamente a cada atestado.
- Preserve `[PREENCHER]` para qualquer campo ainda não configurado. Não invente nomes, registros, endereços, contatos ou especialidades.
- O local configurado é o padrão do cabeçalho. Se a consulta tiver ocorrido em outro local e isso for informado, use o local do caso e sinalize eventual conflito.
- Se a skill for usada em outro local ou por outro profissional, o médico deve editar a configuração ou informar a exceção para aquele atestado.
- Não inclua contato institucional se estiver vazio ou não for desejado.

## Quando usar

- O médico digita `/atestado` com os dados do paciente.
- O médico pede atestado, declaração de afastamento ou de repouso com esses dados.

Não use quando o pedido for prontuário, evolução ou receita (há skills próprias para isso).

## Entrada

Formato livre. Exemplo:

`/atestado Maria Souza, nascimento 14/02/1990, consulta às 11:05 de 27/03/2026, CID J01, 3 dias a partir de 27/03/2026, medicação específica sim`

Extraia os campos abaixo. Campos marcados como obrigatórios impedem a geração: se faltarem, pergunte antes de montar o texto.

| Campo | Obrigatório | Regra |
|---|---|---|
| Nome completo | Sim | Como informado pelo médico |
| Data de nascimento | Sim | dd/mm/aaaa |
| Sexo do paciente | Sim | Define "a paciente" ou "o paciente". Se não informado, pergunte. |
| Data e hora da consulta | Sim | Hora no formato `11:05h` |
| Número de dias | Sim | Inteiro, maior que zero |
| Data de início do afastamento | Não | Se faltar, use a data da consulta e avise |
| CID-10 | Não | Código informado: use. Só o nome da doença: faça a busca da seção seguinte. |
| Medicação específica | Não | "sim" marca o X; ausência deixa em branco |

Confira a coerência antes de gerar: data de nascimento anterior à consulta, data de início não anterior à consulta e número de dias dentro de um intervalo plausível para o quadro. Se algo não fizer sentido, pergunte.

## Busca de CID-10

Use a busca somente quando a doença vier por nome e não por código.

1. Pesquise com `web_search` no modo standard: `CID-10 {doença} DATASUS` ou `CID-10 {doença} OPAS`. Fontes aceitas: DATASUS (Ministério da Saúde) e OPAS/OMS. Não use blogs, resumos sem fonte nem sites de terceiros como referência final.
2. Abra a página oficial encontrada com `web_fetch` (só é possível abrir URLs que surgiram na busca) e localize a categoria exata.
3. Escolha o código mais específico da categoria. Se houver dúvida entre dois códigos, apresente os dois ao médico e não decida sozinho.
4. Registre na linha de conferência a fonte e o código escolhido.
5. Se não houver código seguro, deixe `CID-10: [PREENCHER]` e avise. Nunca complete de memória.

Se a doença vier em vários termos (ex.: "dor nas costas, lombalgia"), pesquise cada um e liste os códigos na ordem informada, separados por vírgula.

## Cálculo do período

- O dia de início conta como dia 1.
- Data final = início + (dias − 1).
- Exemplo: 3 dias a partir de 27/03/2026 → 27, 28 e 29/03/2026, então o fim é 29/03/2026.
- Número por extenso seguido do número entre parênteses com dois dígitos: `um (01) dia`, `dois (02) dias`, `três (03) dias`, `dez (10) dias`.

## Modelo (redação fixa)

Não altere o texto abaixo. Só substitua os campos entre chaves.

```
ATESTADO MÉDICO

Declaro, para os devidos fins, que {ARTIGO} paciente acima identificado(a) foi atendido(a) em consulta médica às {HORA} do dia {DATA_CONSULTA} e encontra-se clinicamente inapto(a) para suas atividades habituais, necessitando de {DIAS_EXTENSO} ({DIAS_NUM}) {DIA_OU_DIAS} de afastamento a partir de {DATA_INICIO}, para fins de repouso e recuperação de sua saúde.

CID-10: {CID}

Este atestado é emitido conforme os princípios éticos e normativos do Conselho Federal de Medicina (CFM), respeitando a confidencialidade das informações médicas e garantindo os direitos do paciente.

ORIENTAÇÕES ADICIONAIS:
( {X_OU_ESPACO} ) Uso de medicação específica conforme orientação/prescrição médica

Atesto a veracidade das informações acima, estando este documento assinado digitalmente conforme as normas vigentes.

Local e Data: {CIDADE}/{UF_CIDADE}, {DATA_EMISSAO_EXTENSO}

Médico Responsável: {NOME_MEDICO}
CRM {CRM}/{UF}
```

Preenchimento dos campos:

- `{ARTIGO}`: "a paciente" para feminino, "o paciente" para masculino. Com o gênero, o adjetivo muda: "atendida"/"atendido", "inapta"/"inapto", "identificada"/"identificado".
- `{HORA}`: horário com `h`, por exemplo `11:05h`.
- `{DATA_CONSULTA}` e `{DATA_INICIO}`: dd/mm/aaaa.
- `{DIA_OU_DIAS}`: "dia" quando o número for 1; "dias" nos demais casos.
- `{DATA_EMISSAO_EXTENSO}`: por extenso, por exemplo "27 de março de 2026". Use a data da consulta, salvo indicação diferente do médico.
- `{CID}`: código ou códigos. Se o paciente pedir sigilo do CID, remova a linha inteira, depois de confirmar com o médico.
- Antes do título, inclua o cabeçalho institucional configurado, omitindo campos vazios, seguido de `Paciente: {NOME}` e `Data de nascimento: {DATA_NASC}`. A frase "o paciente acima identificado" depende das linhas do paciente. Se o caso indicar outro local, use o local informado para a consulta.
- `{CIDADE}` e `{UF_CIDADE}` usam a cidade/UF configurada, salvo indicação de outro local para o caso.
- `{NOME_MEDICO}`, `{CRM}` e `{UF}` usam os dados profissionais configurados. Campos não configurados permanecem como `[PREENCHER]`.

## Alertas (fora do texto do atestado)

Mostre ao médico, depois do bloco de código, apenas os alertas que se aplicam ao caso:

- A frase de assinatura digital só tem validade com certificado ICP-Brasil. Se o atestado for impresso e assinado à mão, sugira retirar ou trocar essa frase.
- Se a data da consulta for diferente de hoje, confirme as datas antes de imprimir.
- Se o CID foi buscado automaticamente, confirme o código com o médico antes da emissão.
- Confira a norma do CFM sobre atestados vigente antes de emitir em série.

## Formato da resposta

1. O atestado completo, dentro de um bloco de código, para copiar.
2. Uma linha de conferência com: período (início e fim), fonte do CID (ou "informado pelo médico"), campos `[PREENCHER]` pendentes e alertas aplicáveis.

Nada além disso. Sem introdução, sem conclusão, sem explicar o processo.

## Exemplo

**Entrada:** `/atestado Maria Souza, nascimento 14/02/1990, consulta às 11:05 de 27/03/2026, CID J01, 3 dias a partir de 27/03/2026, medicação específica sim`

**Saída:**

```
[PREENCHER]
[PREENCHER]
[PREENCHER]/[PREENCHER]

Paciente: Maria Souza
Data de nascimento: 14/02/1990

ATESTADO MÉDICO

Declaro, para os devidos fins, que a paciente acima identificada foi atendida em consulta médica às 11:05h do dia 27/03/2026 e encontra-se clinicamente inapta para suas atividades habituais, necessitando de três (03) dias de afastamento a partir de 27/03/2026, para fins de repouso e recuperação de sua saúde.

CID-10: J01

Este atestado é emitido conforme os princípios éticos e normativos do Conselho Federal de Medicina (CFM), respeitando a confidencialidade das informações médicas e garantindo os direitos do paciente.

ORIENTAÇÕES ADICIONAIS:
( X ) Uso de medicação específica conforme orientação/prescrição médica

Atesto a veracidade das informações acima, estando este documento assinado digitalmente conforme as normas vigentes.

Local e Data: [PREENCHER]/[PREENCHER], 27 de março de 2026

Médico Responsável: [PREENCHER]
CRM [PREENCHER]/[PREENCHER]
```

**Conferência:** período 27/03/2026 a 29/03/2026 (3 dias, contagem inclusiva). CID J01 informado pelo médico. Pendentes: preencher os campos de configuração ainda não definidos. Alerta: a frase de assinatura digital exige certificado ICP-Brasil; se a assinatura for manuscrita, ajuste a frase.

## Erros comuns

| Erro | Correção |
|---|---|
| Escrever "dia(s)" no texto | Use "dia" para 1 e "dias" para os demais casos |
| Escrever "(01)" para três dias | Use o número real: "três (03) dias" |
| Gerar sem saber o sexo do paciente | Pergunte. O artigo e os adjetivos dependem disso. |
| Escolher CID entre dois códigos sem consultar | Apresente os dois ao médico |
| Completar CID de memória | Busque na fonte oficial ou deixe `[PREENCHER]` |
| Usar `site:` na busca sem pedido do médico | Use termos simples: `CID-10 {doença} DATASUS` |
| Explicar o processo ou acrescentar introdução | Entregue só o atestado, a conferência e os alertas |
