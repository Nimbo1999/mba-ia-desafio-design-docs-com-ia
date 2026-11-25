# Criação da skill de ADR

## Contexto

Esse projeto faz parte de um desafio acadêmico sobre Design Docs. O objetivo é criar documentos de design com base em uma reunião virtual entre as engenheiros, POs, desse projeto. A transcrição da reunião está disponível no arquivo @TRANSCRICAO.md.

## Requisitos

### ADR

Produza entre 5 e 8 ADRs em arquivos separados dentro de `docs/adrs/`, nomeados no formato `ADR-NNN-titulo-em-kebab-case.md` (ex: `ADR-001-outbox-no-mysql.md`).

Cada ADR deve seguir o formato MADR (ou variante padrão) com no mínimo as seções: Status, Contexto, Decisão, Alternativas Consideradas (pelo menos 1 alternativa real discutida ou plausível), Consequências (positivas e negativas, com trade-off explícito).

Pelo menos 1 ADR deve referenciar explicitamente arquivos, módulos ou padrões do código existente.

O conjunto de ADRs deve cobrir, no mínimo, 5 das 6 decisões principais discutidas na reunião:

- Padrão Outbox no MySQL
- Política de retry com backoff e DLQ
- Autenticação HMAC-SHA256 com secret por endpoint
- Garantia at-least-once com `X-Event-Id`
- Worker em processo separado em polling
- Reuso dos padrões existentes do projeto

Decisões técnicas secundárias (formato de payload, timeouts, headers, entre outras) podem virar ADRs adicionais ou ficar apenas no FDD, conforme você considerar mais adequado.

---

Crie uma skill que define o processo de criação de um documento ADR que siga os requisitos acima. Requisitos para a skill:

1. A skill deve ser clara e deve seguir os requisitos da ADR.
2. O documento ADR deve possuir todas as seções obrigatórias.
3. O agente deve consultar o código existente e a transcrição da reunião antes de escrever o ADR.
4. Evite palavras complexas e mantenha os padrões de uma leitura rápida e de fácil entendimento.
5. Cada item do ADR deve ter um ID visível, seguindo o catálogo da skill `to-tracker`: `ADR-NNN` (decisão), `ADR-NNN-ALT-NN` (alternativa), `ADR-NNN-TO-NN` (trade-off).
6. Ao terminar de criar ou atualizar um ADR, invoque a skill `to-tracker` para atualizar o `docs/TRACKER.md`.
