# Criação da skill de ADR

## Contexto

Esse projeto faz parte de um desafio acadêmico sobre Design Docs. O objetivo é criar documentos de design com base em uma reunião virtual entre as engenheiros, POs, desse projeto. A transcrição da reunião está disponível no arquivo @TRANSCRICAO.md.

## Requisitos

### Tracker de Rastreabilidade

Produza o arquivo `docs/TRACKER.md`, uma tabela markdown que mapeia cada item registrado nos seus documentos à origem na transcrição ou no código. O tracker funciona como uma referência cruzada: permite que qualquer leitor entenda de onde veio cada decisão, requisito ou restrição, e garante que a documentação permaneça alinhada com o que foi efetivamente discutido e com o que existe no código.

O tracker É uma exigência específica deste desafio que ajuda a manter a integridade da documentação contra implementações equivocadas, sem base e fontes inexistentes.

Formato obrigatório da tabela:

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
|  |  |  |  |  |  |

Onde:

- **ID**: identificador único do item (ex: PRD-FR-01, RFC-ALT-02, FDD-CONTRATO-03, ADR-002)
- **Documento**: arquivo onde o item aparece (`docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/adrs/ADR-002-...md`)
- **Tipo**: Requisito Funcional, Requisito Não Funcional, Decisão, Restrição, Trade-off, entre outros
- **Conteúdo (resumo)**: descrição de uma linha do item
- **Fonte**: `TRANSCRICAO` ou `CODIGO`
- **Localização**: para `TRANSCRICAO`, timestamp + nome do falante (ex: `[09:17] Diego`). Para `CODIGO`, caminho do arquivo (ex: `src/modules/orders/order.service.ts`).

Cobertura mínima: pelo menos 80% dos itens identificáveis nos seus documentos devem ter linha correspondente no tracker.

---

Crie uma skill que define o processo de criação de um documento Tracker que siga os requisitos acima. Requisitos para a skill:

1. A skill deve ser clara e deve seguir os requisitos do documento Tracker.
2. O documento Tracker deve possuir todas as seções obrigatórias.
3. O agente deve consultar o código existente e a transcrição da reunião antes de escrever o Tracker.
4. Evite palavras complexas e mantenha os padrões de uma leitura rápida e de fácil entendimento.
5. Essa skill deve ser invocada sempre que o agente estiver escrevendo um dos seguintes documentos:
 a. PRD
 b. RFC
 c. FDD
 d. ADR
6. Caso um documento desse tipo ja existir, a criação de um novo documento não deve ser feita. Utilize o documento existente realizando a atualização do mesmo para adicionar/remover/atualizar conteúdos desse documento.
