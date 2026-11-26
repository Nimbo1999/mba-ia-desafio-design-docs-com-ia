# Criação da skill de RFC

## Contexto

Esse projeto faz parte de um desafio acadêmico sobre Design Docs. O objetivo é criar documentos de design com base em uma reunião virtual entre as engenheiros, POs, desse projeto. A transcrição da reunião está disponível no arquivo @TRANSCRICAO.md.

## Requisitos

### 2. RFC da feature

Produza o arquivo `docs/RFC.md` com a proposta técnica da solução, no formato de um documento submetido à equipe para revisão. O RFC opera em nível de arquitetura: apresenta a abordagem escolhida, as alternativas que foram colocadas na mesa e as questões deixadas em aberto. É um documento conciso (2 a 4 páginas); o detalhamento de implementação fica no FDD. Deve seguir o formato apresentado no curso e incluir, no mínimo:

- Metadados (autor, status, data, revisores);
- Resumo executivo (TL;DR) da proposta
- Contexto e problema
- Proposta técnica (visão geral da solução, sem descer ao detalhe de implementação do FDD)
- Alternativas consideradas (pelo menos 2 alternativas reais discutidas e descartadas, cada uma com o trade-off que levou ao descarte)
- Questões em aberto (Pontos levantados que não foram decididos ou que foram adiados)
- Impacto e riscos
- Decisões relacionadas (links para os ADRs correspondentes)

O RFC não deve duplicar o detalhamento do FDD. Ele responde "o que propomos e por quê"; o "como construir" em detalhe fica no FDD.

---

Crie uma skill que define o processo de criação de um documento RFC que siga os requisitos apontados acima e os seguintes items:

1. A skill deve ser clara e deve seguir os requisitos do documento RFC.
2. O documento RFC deve possuir todas as seções obrigatórias descritas acima.
3. Evite palavras complexas e mantenha os padrões de uma leitura rápida e de fácil entendimento.
4. Adicione um exemplo ou template simples de um documento RFC como uma forma de few-shot prompting para o agente.
5. Utilize a skill /superpowers:writing-skills para escrever a skill.
6. A skill deve ser escrita no projeto, em `.claude/skills/to-rfc/SKILL.md`.
7. A skill deve ser invocada no chat automaticamente caso o usuário mencionar que precisa criar um document RFC, portando adicione uma descrição da skill que ajude com esse recurso
