---
name: to-adr
description: Use quando for criar, atualizar, substituir ou revisar um ADR (Architecture Decision Record) em docs/adrs/, ou registrar uma decisão de arquitetura em Markdown.
---

# Como escrever um ADR

Um ADR registra **uma decisão** de arquitetura: o que foi decidido, por quê e a que custo. Quem lê deve entender tudo em poucos minutos.

Esta skill trata só de **como escrever o arquivo**.

## Arquivo

- **Caminho:** `docs/adrs/ADR-NNN-titulo-em-kebab-case.md`. Use este formato mesmo que outro arquivo da pasta sugira um diferente.
- **Número:** `NNN` tem três dígitos e é o próximo da sequência. Número usado não volta.
- **Título no kebab-case:** minúsculas, sem acentos, curto, dizendo a decisão (`cache-do-catalogo-em-memoria`).
- **Um arquivo por decisão.** Se houver "e também decidimos", são dois ADRs.

## Antes de criar

Veja os ADRs que já existem em `docs/adrs/`.

| Situação | O que fazer |
| --- | --- |
| Não há ADR sobre o assunto | Crie um novo |
| Há ADR da mesma decisão | Atualize esse arquivo |
| A decisão mudou para outra | Crie um novo e mude o status do antigo para `Substituído por ADR-NNN` |

## Escrita

- No máximo ~60 linhas.
- Frases curtas e palavras simples.
- Detalhe de implementação (contratos, schemas, passo a passo) fica no FDD.
- O ADR fala só da decisão. Não cite reuniões, transcrições, falas, horários, nomes de pessoas nem de onde veio a informação.
- Escreva apenas o que você sabe. Se faltar conteúdo para uma seção obrigatória, avise o usuário em vez de inventar.

## IDs

Todo item abaixo leva o ID visível, em negrito, no começo da linha. `NNN` é o número do ADR.

| ID | Item |
| --- | --- |
| `ADR-NNN` | A decisão |
| `ADR-NNN-ALT-NN` | Cada alternativa considerada |
| `ADR-NNN-TO-NN` | Cada trade-off |

Ao atualizar, mantenha os IDs. Item novo recebe o número seguinte ao maior já usado. ID removido não volta.

## Seções

Todas são obrigatórias, nesta ordem. O exemplo é **fictício** (sistema de uma biblioteca): copie o formato, nunca o conteúdo.

### Título

`# ADR-NNN — Decisão em poucas palavras`

```markdown
# ADR-042 — Cache do catálogo em memória
```

### Status

Um destes valores: `Proposto` (ainda em discussão), `Aceito` (decidido) ou `Substituído por ADR-NNN`.

```markdown
## Status

Aceito
```

### Contexto

**Inclua:** o problema, o que força uma decisão agora e os limites que valem (prazo, equipe, infraestrutura, metas). De 3 a 6 linhas.

**Não inclua:** a solução escolhida nem as alternativas.

```markdown
## Contexto

A busca do catálogo consulta o banco a cada acesso e passa de 2 segundos nos horários de pico.
O catálogo muda poucas vezes por dia. A meta é responder em menos de 500 ms.
A equipe é pequena e não quer operar um serviço novo.
```

### Decisão

**Inclua:** o que será feito, em voz ativa ("Vamos..."), com os parâmetros que definem a escolha e o motivo principal.

**Não inclua:** "podemos" ou "talvez", nem código.

```markdown
## Decisão

**ADR-042** — Vamos guardar o catálogo em um cache na memória da própria aplicação, com validade de 10 minutos.
Isso atinge a meta de tempo sem adicionar infraestrutura.
```

### Alternativas Consideradas

**Inclua:** pelo menos uma opção real, que alguém poderia mesmo ter escolhido. Diga o que ela é e por que foi descartada.

**Não inclua:** opção absurda só para preencher a seção, nem a própria decisão.

```markdown
## Alternativas Consideradas

- **ADR-042-ALT-01** — Cache em um servidor Redis. Descartada: exige operar um serviço novo.
- **ADR-042-ALT-02** — Criar índices no banco. Descartada: a busca por texto continuaria acima da meta.
```

### Consequências

Três subseções, todas obrigatórias:

- **Positivas:** o que melhora. Sem ID.
- **Negativas:** o que piora, o risco ou a limitação aceita. Sem ID. Sempre há pelo menos uma.
- **Trade-offs:** a troca feita, no formato "Ganhamos X, pagamos Y". Com ID. Pelo menos um.

```markdown
## Consequências

### Positivas

- A busca responde em menos de 500 ms.
- Nenhum serviço novo para operar.

### Negativas

- Um livro novo pode levar até 10 minutos para aparecer.
- Cada instância da aplicação mantém sua própria cópia.

### Trade-offs

- **ADR-042-TO-01** — Ganhamos operação simples, pagamos com dados desatualizados por até 10 minutos.
```

### Referências

**Inclua:** os arquivos, módulos ou padrões do código ligados à decisão, e os ADRs relacionados (com link). Diga em uma frase a relação de cada um.

**Regras dos caminhos:**

- Todo caminho citado, em qualquer seção, precisa existir no repositório. Confira antes de escrever.
- Arquivo planejado leva a marca `(a criar)` logo depois do caminho.
- Se não houver nada a citar, escreva `Nenhuma.`

```markdown
## Referências

- `src/catalog/catalog.service.ts`: faz a busca que passará a usar o cache.
- `src/catalog/catalog.cache.ts` (a criar): guardará o cache.
- [ADR-007](ADR-007-busca-no-banco.md): define como o catálogo é consultado.
```

Os caminhos e links acima também são fictícios.

## Confira antes de terminar

- [ ] Nome do arquivo no formato certo e número sem repetição.
- [ ] Uma única decisão, em no máximo ~60 linhas.
- [ ] Título, Status, Contexto, Decisão, Alternativas Consideradas, Consequências (Positivas, Negativas, Trade-offs) e Referências.
- [ ] Decisão, alternativas e trade-offs com ID visível e sem repetição.
- [ ] Pelo menos uma alternativa, uma consequência negativa e um trade-off.
- [ ] Nenhuma menção a reunião, transcrição, pessoas ou horários.
- [ ] Todo caminho existe ou está marcado como `(a criar)`.

## Depois de escrever

Depois de criar ou atualizar um ou mais ADRs, invoque a skill `to-tracker` para atualizar o `docs/TRACKER.md`.
