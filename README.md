# Claude Médicas Skills

Skills para apoiar médicos na preparação de **rascunhos de prontuário e atestado médico** a partir das informações fornecidas pelo profissional. O repositório atualmente contém duas skills; não é uma ferramenta de diagnóstico nem um sistema de emissão automática de documentos.

## Skills disponíveis

| Skill | Quando usar | O que faz |
| --- | --- | --- |
| [`prontuario`](skills/prontuario.md) | `/prontuario` ou pedido de prontuário, evolução clínica ou registro de consulta | Organiza os dados da consulta em um rascunho no modelo definido, identifica lacunas e permite uma rodada de complementação pelo médico antes de gerar o documento. |
| [`atestado`](skills/atestado.md) | `/atestado` ou pedido de rascunho de atestado ou declaração de afastamento | Prepara um rascunho conforme o modelo da skill, após confirmar os dados e a decisão clínica. Calcula o período de afastamento e trata o CID-10 conforme a autorização do paciente. |

Consulte cada arquivo para conhecer o fluxo, os dados necessários e o formato de saída:

- [Prontuário](skills/prontuario.md)
- [Atestado](skills/atestado.md)

## Configuração do atestado

Antes de usar a skill `atestado`, cada médico deve substituir no arquivo os campos de configuração pelo próprio nome, CRM, UF e cidade/UF padrão. Não use dados profissionais de outra pessoa nem invente identificadores; campos ausentes devem permanecer como `[PREENCHER]` e ser sinalizados para conferência.

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
claude-medicas-skills/
├── LICENSE
├── README.md
└── skills/
    ├── atestado.md
    └── prontuario.md
```

## Contribuições

Contribuições são bem-vindas. Ao propor uma nova skill ou alteração, documente sua finalidade, os dados necessários, os limites de uso e as etapas de revisão profissional. Priorize segurança do paciente, privacidade, clareza sobre incertezas e supervisão médica.

## Licença

Este projeto está sob a licença definida em [LICENSE](LICENSE).
