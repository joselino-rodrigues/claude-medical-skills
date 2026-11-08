# Como contribuir

Obrigado pelo interesse em contribuir com o **Claude Medical Skills**. O projeto reúne instruções para apoiar profissionais de saúde na elaboração de rascunhos documentais. Toda contribuição deve preservar a revisão e a responsabilidade do profissional de saúde.

## Antes de propor uma mudança

1. Consulte as skills existentes e as issues abertas para evitar duplicidade.
2. Para uma nova skill, abra uma issue usando o modelo [Proposta de nova skill](.github/ISSUE_TEMPLATE/skill-proposal.md).
3. Explique o problema atendido, quem usará a skill, quando ela deve ou não ser acionada, quais informações precisa e qual documento deve produzir.
4. Discuta o escopo antes de implementar mudanças clínicas ou ampliar a finalidade de uma skill existente.

As propostas de encaminhamento médico, declaração de comparecimento e relatório médico administrativo estão sendo avaliadas como possibilidades para uma próxima versão. A inclusão no milestone `v1.2` não garante que uma proposta será implementada ou fará parte da release.

## Segurança, privacidade e conteúdo clínico

- Não inclua dados reais ou identificáveis de pacientes em issues, pull requests, exemplos ou arquivos do repositório. Use exemplos sintéticos.
- Não crie instruções que façam a IA decidir diagnóstico, tratamento, prescrição, urgência, aptidão para alta ou outra conduta profissional.
- Não invente informações ausentes. A skill deve distinguir dados informados, lacunas, hipóteses e decisões confirmadas pelo profissional.
- Minimize os dados pessoais e clínicos solicitados e exibidos.
- Quando uma proposta envolver conteúdo clínico, descreva limites de uso e pontos que exigem revisão do profissional responsável.
- Não apresente o material como substituto de avaliação clínica, protocolos institucionais ou normas vigentes.

## Padrão para skills

Cada arquivo em `skills/` deve:

- ter front matter com `name` e uma descrição clara de quando usar e quando não usar;
- explicar finalidade, dados necessários, fluxo de interação e comportamento diante de lacunas ou contradições;
- definir regras de precisão, privacidade, supervisão profissional e limites da skill;
- especificar o formato de saída e incluir exemplos sintéticos coerentes com as regras;
- evitar duplicação, instruções contraditórias e escopo que se sobreponha sem justificativa às skills existentes.

## Enviar uma pull request

1. Crie uma branch a partir de `main`.
2. Mantenha a mudança focada e atualize o README quando adicionar ou renomear uma skill.
3. Na descrição da pull request, informe a necessidade atendida, o que mudou, os limites definidos e como verificou a consistência do documento.
4. Confirme que exemplos são fictícios, links estão corretos e os campos de configuração não contêm dados profissionais de terceiros.
5. Solicite revisão antes de considerar a contribuição pronta para publicação.

Uma pull request pode ser ajustada, adiada ou não aceita se houver risco clínico, privacidade insuficiente, escopo pouco claro ou sobreposição com outra skill.

## Versões e releases

O milestone `v1.2` organiza propostas em avaliação; o escopo final será definido após discussão e revisão. Uma skill só deve ser incluída em uma release quando seu conteúdo e seus exemplos tiverem sido revisados e o README e o pacote ZIP correspondente estiverem atualizados.
