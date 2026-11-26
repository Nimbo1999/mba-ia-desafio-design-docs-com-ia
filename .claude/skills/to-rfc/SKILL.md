---
name: to-rfc
description: Use quando for criar, atualizar ou revisar o RFC (Request for Comments) em docs/RFC.md, ou escrever a proposta técnica de uma feature para a revisão da equipe.
---

# Como escrever um RFC

Um RFC é a **proposta técnica** de uma feature, enviada para a equipe revisar. Ele responde a duas perguntas: **o que propomos e por quê**. Quem lê deve entender a proposta em poucos minutos.

O RFC fica no nível de arquitetura. O **como construir**, em detalhe, fica no FDD.

Esta skill trata só de **como escrever o arquivo**.

## Arquivo

- **Caminho:** `docs/RFC.md`. É um arquivo único.
- **Primeira escrita:** se o arquivo só tiver título ou comentário, troque esse conteúdo pela estrutura abaixo.
- **Atualização:** edite o arquivo no lugar. Não crie um segundo RFC.

## Escrita

- Entre 2 e 4 páginas, ou seja, no máximo ~200 linhas.
- Frases curtas e palavras simples.
- O corpo do RFC fala só da proposta. Não cite reuniões, transcrições, falas nem horários. Nomes de pessoas aparecem só nos Metadados.
- Escreva apenas o que você sabe. Se faltar conteúdo para uma seção obrigatória, avise o usuário em vez de inventar.

### O que fica no FDD, e não no RFC

| No RFC (o quê e por quê) | No FDD (como construir) |
| --- | --- |
| "Retry com backoff e fila de falhas" | Número de tentativas, intervalos, timeouts |
| "Uma tabela guarda os eventos pendentes" | Colunas, tipos, índices, migração |
| "O cliente recebe um aviso assinado" | Endpoint, payload, headers, exemplo de JSON |
| "Um processo separado faz o envio" | Passo a passo, código, nomes de funções |

Se o texto tem números exatos, código ou campos de payload, ele é do FDD.

## IDs

Todo item abaixo leva o ID visível, em negrito, no começo da linha. `NN` tem dois dígitos (`01`, `02`...).

| ID | Item | Seção |
| --- | --- | --- |
| `RFC-DEC-NN` | Cada decisão da proposta | Proposta técnica |
| `RFC-ALT-NN` | Cada alternativa descartada | Alternativas consideradas |
| `RFC-QA-NN` | Cada questão em aberto | Questões em aberto |
| `RFC-TO-NN` | Cada trade-off da proposta | Impacto e riscos |

Ao atualizar, mantenha os IDs. Item novo recebe o número seguinte ao maior já usado. ID removido não volta.

## Seções

Todas são obrigatórias, nesta ordem. O exemplo é **fictício** (sistema de uma biblioteca): copie o formato, nunca o conteúdo. Juntos, os trechos formam um RFC completo.

### Título

`# RFC — Nome da feature`

```markdown
# RFC — Aviso de livro disponível
```

### Metadados

**Inclua:** autor (nome e papel), status, data e revisores (nome e papel).

O status é um destes valores: `Rascunho`, `Em revisão`, `Aceito` ou `Rejeitado`. A data é a do dia em que o documento é criado ou atualizado, no formato `AAAA-MM-DD`.

```markdown
## Metadados

| Campo | Valor |
| --- | --- |
| Autor | Ana (Tech Lead) |
| Status | Em revisão |
| Data | 2026-03-10 |
| Revisores | Pedro (Product Manager), Júlia (Engenheira Sênior) |
```

### Resumo (TL;DR)

**Inclua:** o problema e a proposta, em 2 a 4 linhas. Quem ler só esta seção deve saber o que está sendo proposto.

**Não inclua:** alternativas, detalhes ou números exatos.

```markdown
## Resumo (TL;DR)

Leitores que reservam um livro emprestado não sabem quando ele volta.
Propomos avisar o leitor por e-mail quando o livro for devolvido.
O aviso sai de um processo separado, sem atrasar a devolução no balcão.
```

### Contexto e problema

**Inclua:** como funciona hoje, qual é a dor, quem sofre com ela e os limites que valem (prazo, equipe, infraestrutura, metas). De 4 a 8 linhas.

**Não inclua:** a solução proposta.

```markdown
## Contexto e problema

Hoje o leitor reserva um livro e precisa voltar ao site várias vezes para saber se ele voltou.
Muitos desistem, e o livro fica parado na estante com a reserva vencida.
A devolução no balcão já faz várias tarefas e precisa continuar rápida.
A equipe é pequena e não quer operar um serviço novo.
A meta é avisar o leitor em até 5 minutos após a devolução.
```

### Proposta técnica

**Inclua:** uma visão geral da solução em 1 ou 2 frases. Depois, cada decisão de arquitetura com ID, o que será feito e o motivo principal. Quando a decisão tiver ADR, termine a linha com o link.

**Não inclua:** detalhes de implementação (veja a tabela "O que fica no FDD").

```markdown
## Proposta técnica

A devolução registra que há um aviso a enviar. Um processo separado lê esses registros e envia os e-mails.

- **RFC-DEC-01** — Guardar os avisos pendentes em uma tabela do banco atual, na mesma transação da devolução. Assim nenhum aviso se perde. ([ADR-003](adrs/ADR-003-avisos-pendentes-no-banco.md))
- **RFC-DEC-02** — Enviar os avisos em um processo separado da aplicação. Um e-mail lento não trava o balcão. ([ADR-004](adrs/ADR-004-processo-separado-de-envio.md))
- **RFC-DEC-03** — Tentar de novo quando o envio falhar e separar os avisos que falharem sempre. ([ADR-005](adrs/ADR-005-novas-tentativas-de-envio.md))
```

### Alternativas consideradas

**Inclua:** pelo menos 2 opções reais, que alguém poderia mesmo ter escolhido. Diga o que cada uma é e o trade-off que levou ao descarte.

**Não inclua:** opção absurda só para preencher a seção, nem a própria proposta.

```markdown
## Alternativas consideradas

- **RFC-ALT-01** — Enviar o e-mail durante a devolução. Descartada: a devolução ficaria lenta e falharia quando o serviço de e-mail caísse.
- **RFC-ALT-02** — Usar uma fila externa, como RabbitMQ. Descartada: entrega mais rápido, mas exige operar um serviço novo.
```

### Questões em aberto

**Inclua:** os pontos levantados que não foram decididos ou que foram adiados. Para cada um, diga o que falta decidir e, se souber, quem decide ou quando.

**Não inclua:** pontos já decididos.

```markdown
## Questões em aberto

- **RFC-QA-01** — Quanto tempo o leitor tem para retirar o livro depois do aviso? Depende da regra da biblioteca.
- **RFC-QA-02** — Vamos avisar também por SMS? Adiado para depois da primeira versão.
```

### Impacto e riscos

Três partes, todas obrigatórias:

- **Impacto:** o que muda para usuários, para a equipe e para o sistema. Sem ID.
- **Riscos:** o que pode dar errado e como reduzir. Sem ID. Sempre há pelo menos um.
- **Trade-offs:** a troca feita pela proposta, no formato "Ganhamos X, pagamos Y". Com ID. Pelo menos um.

```markdown
## Impacto e riscos

### Impacto

- O leitor recebe o aviso sem precisar voltar ao site.
- A equipe passa a manter um processo novo, além da aplicação.

### Riscos

- Os e-mails podem cair no spam. Reduzir com um remetente verificado.
- A tabela de avisos pode crescer muito. Reduzir apagando avisos antigos.

### Trade-offs

- **RFC-TO-01** — Ganhamos operação simples, pagamos com um aviso que pode levar alguns minutos.
- **RFC-TO-02** — Ganhamos a garantia de que o aviso sai, pagamos com a chance de o leitor receber o mesmo aviso duas vezes.
```

### Decisões relacionadas

**Inclua:** o link para cada ADR ligado à proposta, com uma frase sobre o que ele decide. Os links partem de `docs/`, então usam `adrs/ADR-NNN-titulo.md`.

**Regras dos links:**

- Todo link e todo caminho citado no RFC precisa existir no repositório. Confira os arquivos em `docs/adrs/` antes de escrever.
- Se uma decisão ainda não tem ADR, não crie o link. Avise o usuário quais ADRs estão faltando.
- Se não houver ADR relacionado, escreva `Nenhuma.`

```markdown
## Decisões relacionadas

- [ADR-003](adrs/ADR-003-avisos-pendentes-no-banco.md): avisos pendentes guardados no banco atual.
- [ADR-004](adrs/ADR-004-processo-separado-de-envio.md): envio em um processo separado.
- [ADR-005](adrs/ADR-005-novas-tentativas-de-envio.md): novas tentativas e separação dos avisos com falha.
```

Os nomes, datas e links acima também são fictícios.

## Erros comuns

| Erro | Correção |
| --- | --- |
| Colar endpoint, payload ou schema na Proposta técnica | Descreva a ideia em uma frase. O detalhe vai para o FDD |
| Só uma alternativa, ou alternativa sem motivo do descarte | Pelo menos 2, cada uma com o trade-off que levou ao descarte |
| Questão em aberto que já foi decidida | Mova para a Proposta técnica como `RFC-DEC-NN` |
| Link para um ADR que não existe | Remova o link e avise o usuário |
| Resumo longo, que repete o Contexto | No máximo 4 linhas: problema e proposta |

## Confira antes de terminar

- [ ] O arquivo é `docs/RFC.md` e tem no máximo ~200 linhas.
- [ ] Título, Metadados, Resumo (TL;DR), Contexto e problema, Proposta técnica, Alternativas consideradas, Questões em aberto, Impacto e riscos (Impacto, Riscos, Trade-offs) e Decisões relacionadas, nesta ordem.
- [ ] Os Metadados têm autor, status, data e revisores.
- [ ] Decisões, alternativas, questões em aberto e trade-offs têm ID visível e sem repetição.
- [ ] Pelo menos 2 alternativas, cada uma com o motivo do descarte.
- [ ] Pelo menos um risco e um trade-off.
- [ ] Nenhum detalhe de implementação que pertence ao FDD.
- [ ] Nenhuma menção a reunião, transcrição, falas ou horários fora dos Metadados.
- [ ] Todo link e todo caminho existem no repositório.

## Depois de escrever

Depois de criar ou atualizar o RFC, invoque a skill `to-tracker` para atualizar o `docs/TRACKER.md`.
