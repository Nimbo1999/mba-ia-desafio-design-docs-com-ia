---
name: to-tracker
description: Cria ou atualiza o docs/TRACKER.md, a tabela que liga cada item dos documentos à sua origem na transcrição ou no código. Use SEMPRE que criar ou alterar um PRD, RFC, FDD ou ADR (docs/PRD.md, docs/RFC.md, docs/FDD.md, docs/adrs/*.md), e quando o usuário pedir para montar, revisar ou auditar o tracker.
---

# Tracker de Rastreabilidade

O `docs/TRACKER.md` mostra **de onde veio cada item** dos documentos: de uma fala da reunião (`TRANSCRICAO.md`) ou de um arquivo do código. Se um item não tem origem, ele provavelmente foi inventado. Nesse caso, avise o usuário.

## Regras de ouro

1. **Nunca crie um segundo tracker.** Sempre edite o `docs/TRACKER.md` que já existe. Se ele só tiver título ou comentário, troque esse conteúdo pela estrutura abaixo.
2. **Um ID por linha.** Se o ID já existe, atualize a linha no lugar. Se o item saiu do documento, remova a linha.
3. **IDs não mudam.** Nunca renumere e nunca reaproveite um ID removido.
4. **Sem fonte, sem linha.** Não invente timestamp, falante ou arquivo.
5. **Não edite PRD, RFC, FDD ou ADR.** Esta skill só escreve no `docs/TRACKER.md`. Problemas nos outros documentos são relatados ao usuário.

## Escopo de cada execução

- **Depois de escrever um documento:** atualize só as linhas desse documento.
- **Chamada direta pelo usuário:** faça a auditoria completa de todos os documentos.

## Passo a passo

### 1. Leia antes de escrever

- `TRANSCRICAO.md` inteira: quem falou o quê, e em que horário.
- O código em `src/` e `prisma/`, para achar os arquivos e linhas citados nos documentos.
- O(s) documento(s) em escopo dentro de `docs/`.
- O `docs/TRACKER.md` atual.

### 2. Liste os itens identificáveis

Vira linha:

| Documento | O que vira linha |
| --- | --- |
| PRD | cada requisito funcional, requisito não funcional, restrição, item fora de escopo e métrica de sucesso |
| RFC | cada decisão, alternativa descartada, trade-off e questão em aberto |
| FDD | cada contrato (endpoint, payload, header, tabela), fluxo, ponto de integração com o código e regra (timeout, retry etc.) |
| ADR | a decisão, cada alternativa considerada e cada trade-off principal |

Não vira linha: texto de contexto, introdução ou explicação geral.

Use o ID que está escrito no documento. Se o item não tiver ID, avise o usuário e sugira um ID do catálogo abaixo.

### 3. Ache a fonte de cada item

- **TRANSCRICAO** (tem prioridade): procure a fala que sustenta o item. A Localização fica `[hh:mm] Nome`, exatamente como na transcrição.
- **CODIGO**: use quando o item vem do código existente. A Localização fica `caminho:linha` (ex.: `src/modules/orders/order.service.ts:42`) ou `caminho:início-fim` para um bloco (ex.: `src/modules/orders/order.service.ts:30-58`).
- **Vários locais:** separe com `; ` (ex.: `[09:04] Bruno; [09:12] Diego`). Mantenha uma única Fonte por linha.
- **Sem fonte:** não crie a linha. Guarde o item para o relatório final.

### 4. Escreva o `docs/TRACKER.md`

Use esta estrutura:

```markdown
# Tracker de Rastreabilidade

Liga cada item dos documentos de design à sua origem na reunião (`TRANSCRICAO.md`) ou no código.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
| ADR-001 | `docs/adrs/ADR-001-outbox-no-mysql.md` | Decisão | Usar padrão Outbox em tabela MySQL | TRANSCRICAO | [09:10] Diego |

## Cobertura

- Itens identificáveis: X. Com linha no tracker: Y (Z%).
- Linhas com fonte TRANSCRICAO: N (P%).
- Linhas com fonte CODIGO: M.
```

Regras da tabela:

- **Ordem:** ADRs → RFC → FDD → PRD. Dentro de cada documento, siga a ordem dos IDs.
- **Documento:** o caminho do arquivo, entre crases.
- **Tipo:** só valores da lista fechada abaixo.
- **Conteúdo (resumo):** uma linha, com no máximo ~120 caracteres, palavras simples e sem o caractere `|`.
- **Fonte:** só `TRANSCRICAO` ou `CODIGO`.

### 5. Confira antes de terminar

- [ ] Cada `[hh:mm] Nome` existe na `TRANSCRICAO.md` (confira com grep) e a fala sustenta o item.
- [ ] Cada arquivo citado existe, a linha existe e o trecho cita o que o resumo diz. Se o trecho mudou de lugar, corrija o número da linha. Na auditoria completa, confira todas as linhas `CODIGO`.
- [ ] Pelo menos **80%** dos itens identificáveis têm linha no tracker.
- [ ] Pelo menos **70%** das linhas têm Fonte `TRANSCRICAO`.
- [ ] Pelo menos **5** linhas têm Fonte `CODIGO`.
- [ ] Nada que a reunião descartou aparece como requisito ou decisão. Se aparecer, só pode ser como `Fora de Escopo` ou `Alternativa`.
- [ ] Nenhum ID está repetido.
- [ ] A seção "Cobertura" mostra os números atualizados.

### 6. Relate ao usuário

Mostre de forma curta:

- as linhas adicionadas, atualizadas e removidas;
- os números de cobertura;
- os itens **sem fonte**, com sugestão de ajustar ou remover cada um no documento de origem;
- os itens **sem ID** no documento de origem.

## Catálogo de IDs

| Prefixo | Uso |
| --- | --- |
| `PRD-FR-NN` | Requisito funcional |
| `PRD-NFR-NN` | Requisito não funcional |
| `PRD-RESTR-NN` | Restrição |
| `PRD-FORA-NN` | Fora de escopo |
| `PRD-MET-NN` | Métrica de sucesso |
| `RFC-DEC-NN` | Decisão |
| `RFC-ALT-NN` | Alternativa descartada |
| `RFC-TO-NN` | Trade-off |
| `RFC-QA-NN` | Questão em aberto |
| `FDD-CONTRATO-NN` | Contrato (endpoint, payload, header, tabela) |
| `FDD-FLUXO-NN` | Fluxo |
| `FDD-INTEG-NN` | Integração com o código existente |
| `FDD-REGRA-NN` | Regra (timeout, retry etc.) |
| `ADR-NNN` | A decisão do ADR |
| `ADR-NNN-ALT-NN` | Alternativa considerada no ADR |
| `ADR-NNN-TO-NN` | Trade-off do ADR |

`NN` e `NNN` são números com zeros à esquerda (ex.: `PRD-FR-01`, `ADR-003-ALT-02`).

## Tipos permitidos

Requisito Funcional · Requisito Não Funcional · Decisão · Restrição · Trade-off · Alternativa · Contrato · Fora de Escopo · Questão em Aberto · Risco · Métrica

## Para quem escreve PRD, RFC, FDD ou ADR

1. Escreva o ID ao lado de cada item identificável, usando o catálogo acima (ex.: `**PRD-FR-01** — ...`).
2. Ao terminar de criar ou atualizar o documento, invoque a skill `to-tracker`.
