---
name: markitdown-conversor
description: Converte ficheiros locais do Mac ou páginas web em Markdown através do servidor MCP markitdown (Docker). Usar sempre que o utilizador pedir para converter, extrair texto, ler ou passar a Markdown um PDF, DOCX, XLSX, PPTX, HTML, MTQ, orçamento, caderno de encargos, contrato ou página web, ou quando mencionar markitdown, /workdir ou a pasta MarkItDown-Entrada.
---

# Conversor MarkItDown

## Onde está o servidor

- Servidor MCP `markitdown`, a correr em Docker no Mac do utilizador (imagem `markitdown-mcp:0.0.1a7`).
- Ferramenta: `mcp__markitdown__convert_to_markdown`, com um único argumento `uri`.
- A ferramenta é diferida: carregá-la primeiro com `tool_search` (pesquisa `markitdown`).
- Pasta do Mac `/Users/nuno/Documents/MarkItDown-Entrada` = `/workdir` dentro do servidor, só leitura.

## Procedimento

1. Carregar `mcp__markitdown__convert_to_markdown` com `tool_search`.
2. Construir o URI:
   - Ficheiro local: `file:///workdir/<nome>`. Espaços passam a `%20`, acentos em percent-encoding UTF-8.
   - Página web: o URL `https://` tal como foi dado.
3. Chamar a ferramenta e devolver o Markdown.
4. Se o utilizador quiser o ficheiro, gravar o resultado como `.md` para descarga.

## Regras

- Nunca usar o contentor de execução de código (`bash_tool`, `/mnt/user-data`) para procurar ficheiros de `/workdir`. O contentor não vê o Mac; o servidor markitdown vê.
- Nunca afirmar que o ficheiro não existe sem ter chamado a ferramenta.
- Se o utilizador anexar o ficheiro ao chat, explicar numa frase que o anexo fica na nuvem e pedir que o copie para `Documentos → MarkItDown-Entrada`; se preferir não o fazer, converter a partir do texto extraído e avisar que as tabelas podem perder estrutura.
- Se o nome do ficheiro não for dado, pedir o nome exato. O servidor não lista pastas.

## Erros

| Mensagem | Causa | Ação |
|---|---|---|
| `No such file or directory` | Ficheiro fora da pasta ou nome errado | Pedir para confirmar o nome e a pasta `MarkItDown-Entrada` |
| Ferramenta não encontrada no `tool_search` | Servidor desligado | Pedir para abrir o Docker Desktop e reiniciar a aplicação Claude (Cmd+Q) |
| `File conversion failed` | Formato não suportado ou ficheiro danificado | Indicar o formato e sugerir exportar para PDF ou DOCX |

## Verificação de tabelas

Em MTQs, orçamentos e mapas comparativos, confirmar três artigos do Markdown contra o original (código, quantidade, preço unitário) antes de usar os valores.
