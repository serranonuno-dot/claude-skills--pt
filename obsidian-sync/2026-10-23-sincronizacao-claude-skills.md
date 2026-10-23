---
data: 2026-10-23
origem: claude-skills--pt
tags: [claude, skills, markitdown]
---

# Sincronização: claude-skills--pt

Repositório: `serranonuno-dot/claude-skills--pt`, branch `claude/zen-pasteur-wv59rz`.

## Estado do repositório

Sem alterações por commitar. Histórico relevante:

| Commit | Mensagem |
|---|---|
| `3a85a14` | Adiciona skill `markitdown-conversor` para o servidor MCP markitdown local |
| `2a5d285` | Corrige o URI da skill `markitdown-conversor` para `file:///workdir` |

## Skill `markitdown-conversor`

Ficheiro: `markitdown-conversor/SKILL.md`, com um pacote `markitdown-conversor.zip` ao lado.

- Converte ficheiros locais do Mac e páginas web em Markdown através do servidor MCP `markitdown`, a correr em Docker (imagem `markitdown-mcp:0.0.1a7`).
- Ferramenta: `mcp__markitdown__convert_to_markdown`, com o argumento único `uri`.
- O URI de ficheiros locais é sempre `file:///workdir/<nome>`. O servidor só vê `/workdir`, que corresponde à pasta `MarkItDown-Entrada` no Mac, montada em modo leitura.
- Espaços e acentos no nome passam a percent-encoding UTF-8.
- Em MTQs, orçamentos e mapas comparativos, confirmar três artigos contra o original (código, quantidade, preço unitário) antes de usar os valores.

Erros conhecidos:

- `No such file or directory`: nome ou pasta errados.
- Ferramenta não encontrada: servidor desligado. Abrir o Docker Desktop e reiniciar a aplicação Claude.
- `File conversion failed`: formato não suportado ou ficheiro danificado. Exportar para PDF ou DOCX.

## Pendente

- Nenhum ficheiro por integrar no vault neste momento.
- O comando `/obsidian-master-kit:sync` não corre nesta sessão, porque o contentor não tem acesso a `/documentos/MeuSegundoCerebro`.
