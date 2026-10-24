# Fluxo de trabalho: proposta de obra a partir de pedido de orçamento

## Função

Prepara a proposta completa de obra a partir de um pedido de orçamento recebido por email. Produz rascunhos na pasta `outbox/`. Não envia, não publica, não apaga, não paga e não contacta ninguém.

## Gatilho

Novo email com pedido de orçamento no Gmail. A referência do email e a pasta do projeto ficam registadas em `state/STATE.md`, no campo `processo`.

## Saída

Oito documentos em `outbox/`: `esclarecimentos.md`, `preco.md`, `analise.md`, `cronogramas.md`, `memoria.md`, `abrangente.md`, `resumo.md` e `pss.md`.

O estado entre etapas fica em `state/STATE.md`. Cada execução fica registada em `state/run-log.md`.

## Regras rígidas

1. Nunca envia, publica, apaga, paga nem contacta uma pessoa. Todo o envio passa por aprovação humana.
2. Tudo fica como rascunho em `outbox/`.
3. Número máximo de tentativas: 2 na etapa 1 e 3 nas restantes. Esgotadas as tentativas, o processo para e pergunta se deve parar ou ignorar e registar.
4. Quem produz o documento não o avalia. A verificação é feita numa passagem separada, que lê só o documento de saída e o campo de estado correspondente.
5. Preço em falta não se estima. Uma linha sem preço na tabela fica marcada como SEM PREÇO e a etapa 3 é REPROVADO.
6. A margem e a tabela de preços não se alteram sem registo no `run-log.md`.
7. Cada execução grava no `state/run-log.md`: data, etapas aprovadas, etapas reprovadas com o motivo e pendentes.
8. Não grava fora de `outbox/` e não apaga ficheiros do projeto.
9. Obrigação legal só entra no texto com a data da consulta ao texto consolidado. Sem essa verificação, escreve-se "Não verificado face ao texto em vigor".

## Pastas

- `CLAUDE.md`: este mapa e as regras.
- `.claude/skills/proposta-orcamento/SKILL.md`: procedimento das oito etapas.
- `state/STATE.md`: campos transferidos entre etapas.
- `state/run-log.md`: registo de execuções.
- `outbox/`: rascunhos. Nada sai daqui sem aprovação.
