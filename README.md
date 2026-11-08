# Claude Medical Skills

Skills para apoiar médicos na preparação de **rascunhos de prontuário, atestado médico e relatório de alta** a partir das informações fornecidas pelo profissional. O repositório atualmente contém três skills; não é uma ferramenta de diagnóstico nem um sistema de emissão automática de documentos.

## Skills disponíveis

| Skill | Quando usar | O que faz |
| --- | --- | --- |
| [`prontuario`](skills/prontuario.md) | `/prontuario` ou pedido de prontuário, evolução clínica ou registro de consulta | Organiza os dados da consulta em um rascunho no modelo definido, identifica lacunas e permite uma rodada de complementação pelo médico antes de gerar o documento. |
| [`atestado`](skills/atestado.md) | `/atestado` ou pedido de rascunho de atestado ou declaração de afastamento | Prepara um rascunho conforme o modelo da skill, após confirmar os dados e a decisão clínica. Calcula o período de afastamento e trata o CID-10 conforme a autorização do paciente. |
| [`alta`](skills/alta.md) | `/alta` ou pedido de relatório de alta médica | Organiza dados da internação em um rascunho de relatório de alta, sinalizando lacunas e sem decidir a alta ou definir tratamento. |

Consulte cada arquivo para conhecer o fluxo, os dados necessários e o formato de saída:

- [Prontuário](skills/prontuario.md)
- [Atestado](skills/atestado.md)
- [Relatório de alta médica](skills/alta.md)

## Instalação no Claude

As skills devem ser instaladas na conta Claude de cada usuário. É necessário habilitar **Code execution and file creation** (ou **Create and edit files**, conforme a interface) em **Settings > Capabilities**. Depois, prepare um ZIP separado para cada skill:

```text
prontuario.zip
└── prontuario/
    └── skill.md
```

```text
atestado.zip
└── atestado/
    └── skill.md
```

```text
alta.zip
└── alta/
    └── skill.md
```

Use o conteúdo do arquivo correspondente em `skills/` como `skill.md`. O ZIP contém a pasta da skill na raiz, e não apenas o arquivo solto. Em seguida, acesse [Customize > Skills](https://claude.ai/customize/skills), escolha **+ Create skill > Upload a skill**, envie cada MD e ative as skills na lista. A disponibilidade pode variar conforme o plano e as configurações da organização.

Com as skills ativadas, você pode solicitá-las explicitamente no chat usando `/prontuário`, `/atestado` ou `/alta` para gerar o respectivo rascunho. Também é possível descrever a tarefa em linguagem natural; Claude pode selecionar uma skill ativada quando considerar que ela se aplica.

Consulte a documentação oficial do Claude sobre [como criar skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills) e [como usá-las](https://support.claude.com/en/articles/12512180-use-skills-in-claude) para conferir os passos atuais da interface.

## Configuração das skills

Depois de instalar cada skill, edite seu conteúdo na conta Claude e preencha a seção **Configuração permanente após a instalação** com os dados do profissional e do local de atendimento. As skills reutilizam esses dados no cabeçalho e na identificação do profissional, sem solicitá-los a cada documento. Campos não preenchidos permanecem como `[PREENCHER]`; revise a configuração se mudar de local ou profissional. Não use dados profissionais de outra pessoa nem invente identificadores.

## Distribuição

O repositório está configurado para distribuir os arquivos Markdown das skills

Esse pacote contém os arquivos-fonte das skills; para enviá-las à conta Claude, use os ZIPs individuais disponíveis na [release mais recente](https://github.com/joselino-rodrigues/claude-medical-skills/releases/latest).

## Uso responsável

As skills destinam-se exclusivamente ao médico responsável pelo atendimento ou pela emissão do documento. O conteúdo gerado é sempre um **rascunho**: deve ser revisado, corrigido e validado pelo médico, que é responsável por decidir o que será registrado e por assinar o documento antes de incorporá-lo ao prontuário ou entregá-lo.

As instruções das skills exigem, entre outros cuidados:

- Não inventar ou inferir dados clínicos, resultados, tratamentos, decisões médicas ou identificadores profissionais ausentes.
- Distinguir informações relatadas, achados observados e sugestões da IA; registrar lacunas e trechos incertos para revisão.
- Não apresentar hipótese diagnóstica, CID-10 ou sugestão terapêutica como decisão confirmada do médico.
- No atestado, incluir o CID-10 somente após confirmação de que o paciente autorizou e assinou a divulgação; nunca simular a autorização ou assinatura.
- Usar dados pessoais estritamente necessários e observar as regras de privacidade aplicáveis. Evite inserir informações identificáveis desnecessárias.

O material não substitui avaliação clínica, protocolos locais, normas vigentes nem o julgamento e a responsabilidade do médico. Em situações urgentes, a avaliação clínica deve ser priorizada; a documentação não deve atrasá-la.

## Estrutura

```text
claude-medical-skills/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   └── skill-proposal.md
│   └── pull_request_template.md
├── CONTRIBUTING.md
├── LICENSE
├── README.md
└── skills/
    ├── alta.md
    ├── atestado.md
    └── prontuario.md
```

## Contribuições

Contribuições são bem-vindas. Consulte [Como contribuir](CONTRIBUTING.md), use o [modelo de proposta de nova skill](https://github.com/joselino-rodrigues/claude-medical-skills/issues/new?template=skill-proposal.md) e acompanhe o [milestone v1.2](https://github.com/joselino-rodrigues/claude-medical-skills/milestone/1). As propostas em avaliação incluem encaminhamento médico, declaração de comparecimento e relatório médico administrativo; a prioridade e o escopo da versão 1.2 serão definidos após discussão e revisão. Não envie dados reais ou identificáveis de pacientes.

## Licença

Este projeto está sob a licença definida em [LICENSE](LICENSE).
