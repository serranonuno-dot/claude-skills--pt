---
name: proposta-orcamento
description: Prepara a proposta completa de obra a partir de um pedido de orçamento recebido por email. Reúne o processo do projeto, redige esclarecimentos, calcula preços correntes, elabora análise de custos e económica, cronogramas, memória descritiva, proposta abrangente, resumo e PSS. Usar quando chega um pedido de orçamento. Tudo fica em rascunho na outbox, sem envio.
---

# Proposta de obra a partir de pedido de orçamento

Lê primeiro `CLAUDE.md` e `state/STATE.md`. No fim de cada etapa, acrescenta uma linha ao `state/run-log.md`.

## Ordem das etapas

Cada etapa lê os campos de `state/STATE.md` indicados e grava o seu resultado na outbox e no campo de estado. Depois, a verificação corre numa passagem separada, que lê só o documento de saída e o campo de estado. Responde APROVADO ou REPROVADO com o motivo.

### Etapa 1. Reunir processo

- Tentativas máximas: 2.
- Lê: email do pedido de orçamento (Gmail) e a pasta do projeto indicada em `processo.pasta`.
- Prompt: "Lê o email do cliente e lista os ficheiros do projeto recebidos: desenhos, medições, caderno de encargos e cláusulas contratuais, com a data de cada um. Regista tudo no campo `processo` de `state/STATE.md`. Não abras nem alteres os ficheiros."
- Verificação (correspondência): APROVADO se as quatro categorias estão presentes. REPROVADO com a lista do que falta.
- Aprovação humana: não.

### Etapa 2. Redigir esclarecimentos

- Tentativas máximas: 3.
- Lê: campo `processo`.
- Prompt: "Com base no campo `processo`, identifica lacunas e incoerências entre desenhos, medições e caderno de encargos. Para cada uma, escreve um pedido com referência ao documento e ao desenho ou cláusula. Grava em `outbox/esclarecimentos.md` e atualiza o campo `esclarecimentos` como rascunho. Não envies nada."
- Verificação (contém): APROVADO se cada pedido cita documento e desenho ou cláusula. REPROVADO com os pedidos sem referência.
- Aprovação humana: sim. PARE. Salva o rascunho e lista-o em Aguardando aprovação. O envio ao cliente só acontece depois da tua aprovação.

### Etapa 3. Pedir proposta a preços correntes

- Tentativas máximas: 3.
- Lê: campo `processo`, tabela de preços em `preco.referencia` e as respostas aos esclarecimentos em `esclarecimentos`.
- Prompt: "Com base nas medições do campo `processo` e na tabela de preços de referência, atribui um preço unitário a cada linha e calcula o total pela soma das linhas. Se uma linha não tiver preço na tabela, marca-a como SEM PREÇO e não estimes. Regista a data de cada preço. Grava em `outbox/preco.md` e atualiza o campo `preco`."
- Verificação (correspondência): APROVADO se não há linhas SEM PREÇO e o total é igual à soma das linhas. REPROVADO com as linhas sem preço ou o total divergente.

### Etapa 4. Elaborar análise de custos e económica

- Tentativas máximas: 3.
- Lê: campo `preco` e `custos`.
- Prompt: "Com base no campo `preco`, elabora a análise de custos (custo direto, custo indireto, margem) e a análise económica. Usa a margem do campo `custos` e não a alteres. Grava em `outbox/analise.md` e atualiza o campo `custos`."
- Verificação (formato e correspondência): APROVADO se a tabela tem as colunas custo direto, custo indireto, margem e total, e o total de custos é igual ao `preco` mais a margem. REPROVADO com a coluna em falta ou a diferença.

### Etapa 5. Elaborar cronogramas

- Tentativas máximas: 3.
- Lê: caderno de encargos (em `processo`), medições e campo `custos`.
- Prompt: "Com base no caderno de encargos e nas medições, elabora o cronograma de obra e o cronograma financeiro. O prazo e o total financeiro vêm do campo `custos` e não são recalculados à mão. Grava em `outbox/cronogramas.md` e atualiza o campo `cronograma`."
- Verificação (correspondência): APROVADO se o prazo do cronograma de obra é igual ao prazo de `cronograma` e a soma do cronograma financeiro é igual ao total de `custos`. REPROVADO com os valores divergentes.

### Etapa 6. Redigir memória descritiva

- Tentativas máximas: 3.
- Lê: caderno de encargos (em `processo`) e `outbox/cronogramas.md`.
- Prompt: "Redige a memória descritiva com uma secção por capítulo do caderno de encargos, na mesma ordem. Não acrescentes soluções técnicas que não estejam no caderno ou nos desenhos. Grava em `outbox/memoria.md`."
- Verificação (contém): APROVADO se cada capítulo do caderno de encargos tem secção correspondente. REPROVADO com os capítulos sem secção.

### Etapa 7. Compor proposta abrangente e resumo

- Tentativas máximas: 3.
- Lê: todos os documentos da outbox.
- Prompt: "Compõe a proposta abrangente e a proposta resumo a partir dos documentos da outbox. Totais e prazos têm de ser iguais nos dois documentos. O resumo tem menos de 300 palavras. Grava em `outbox/abrangente.md` e `outbox/resumo.md`."
- Verificação (correspondência, com parte visual): APROVADO se totais e prazos coincidem e o resumo tem menos de 300 palavras. REPROVADO com a divergência. A paginação e o layout ficam para a avaliação visual do utilizador a cada execução.

### Etapa 8. Preparar PSS

- Tentativas máximas: 3.
- Lê: desenhos (em `processo`), `outbox/cronogramas.md` e `outbox/memoria.md`.
- Prompt: "Com base nos desenhos, no cronograma de obra e na memória descritiva, elabora o PSS. Para cada fase do cronograma, indica os riscos e a medida preventiva. Antes de citares obrigações legais, confirma o texto consolidado do DL n.º 273/2003 e indica a data da consulta. Se não conseguires, escreve 'Não verificado face ao texto em vigor'. Grava em `outbox/pss.md`."
- Verificação (contém): APROVADO se cada fase tem risco e medida preventiva, e cada obrigação legal tem data de consulta. REPROVADO com uma fase sem medida ou uma obrigação sem data.

## Ponto humano final

Depois da etapa 8, o processo para. Revês os oito documentos da outbox. Só depois da tua aprovação a proposta sai para o cliente, e essa parte é feita por ti.

## Verificação com /goal

Nas etapas de ciclo, a condição de paragem pode ser dada ao Claude Code com /goal. A condição pede prova, e o verificador só lê a conversa.

- Etapa 3: `/goal a tabela de preco.md não tem linhas SEM PREÇO e o total é igual à soma das linhas, mostrado como tabela de conferência, ou pare após 3 turnos`
- Etapa 4: `/goal a análise tem as colunas custo direto, custo indireto, margem e total, e o total de custos é igual ao preço mais a margem, mostrado como tabela, ou pare após 3 turnos`
- Etapa 5: `/goal o prazo do cronograma de obra é igual ao prazo da proposta e a soma do cronograma financeiro é igual ao total, mostrado como tabela de conferência, ou pare após 3 turnos`
- Etapa 6: `/goal cada capítulo do caderno de encargos tem secção na memória descritiva, mostrado como lista de correspondência, ou pare após 3 turnos`
- Etapa 7: `/goal o resumo tem menos de 300 palavras e totais e prazos são iguais nos dois documentos, mostrado com a contagem de palavras e a comparação, ou pare após 3 turnos`
- Etapa 8: `/goal cada fase do cronograma tem risco e medida preventiva, e cada obrigação legal tem data de consulta, mostrado como tabela, ou pare após 3 turnos`

## Como executar

Pede ao Claude Code: `use um fluxo de trabalho para executar a skill proposta-orcamento, etapas 1 a 8, cada uma com a sua verificação e o número máximo de tentativas, parando em cada ponto humano.`

Depois de a execução funcionar uma vez, abre `/workflows` e pressiona `s` para a guardar como comando próprio.

Um fluxo de trabalho não pode parar a meio para te consultar. Cada ponto humano encerra a execução. Para continuar, pede: `retoma o processo proposta-orcamento a partir da etapa seguinte, depois da aprovação`.
