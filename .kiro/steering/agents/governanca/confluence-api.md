---
inclusion: manual
---

# Confluence API — Instruções de Integração | Cogna

Referência para o agente publicar e atualizar documentos de governança no Confluence via API REST.

---

## Autenticação

A API do Confluence usa **Basic Auth** com token de API pessoal.

```
Authorization: Basic base64(email:api_token)
Content-Type: application/json
```

O agente deve solicitar ao DPO (ou ler de variável de ambiente/arquivo de configuração) antes de fazer chamadas:
- **URL base do Confluence** (ex: `https://cogna.atlassian.net`)
- **E-mail do usuário** autenticado
- **Token de API** (gerado em https://id.atlassian.com/manage-profile/security/api-tokens)

Se não houver credenciais configuradas, informar ao DPO:
> "Para publicar no Confluence, preciso das credenciais de API. Configure as variáveis `CONFLUENCE_URL`, `CONFLUENCE_EMAIL` e `CONFLUENCE_TOKEN` no ambiente, ou informe aqui."

---

## Criar página (POST)

```
POST {CONFLUENCE_URL}/wiki/rest/api/content
```

**Body:**
```json
{
  "type": "page",
  "title": "{titulo_documento}",
  "space": {
    "key": "{space_key}"
  },
  "ancestors": [
    {
      "id": "{id_pagina_pai}"
    }
  ],
  "body": {
    "storage": {
      "value": "{conteudo_xhtml}",
      "representation": "storage"
    }
  }
}
```

**Resposta de sucesso (201):** retorna o objeto da página criada com `id` e `_links.webui` (link da página).

**Como obter o `id` da página pai:**
```
GET {CONFLUENCE_URL}/wiki/rest/api/content?title={titulo_pagina_pai}&spaceKey={space_key}
```

---

## Atualizar página existente (PUT)

Para atualizar um documento já publicado (quando o DPO pede ajuste):

```
PUT {CONFLUENCE_URL}/wiki/rest/api/content/{page_id}
```

**Body:**
```json
{
  "type": "page",
  "title": "{titulo_documento}",
  "version": {
    "number": {versao_atual + 1}
  },
  "body": {
    "storage": {
      "value": "{conteudo_xhtml_atualizado}",
      "representation": "storage"
    }
  }
}
```

**Obter versão atual antes de atualizar:**
```
GET {CONFLUENCE_URL}/wiki/rest/api/content/{page_id}
```
Ler `version.number` da resposta e incrementar +1 no PUT.

---

## Converter markdown para Confluence Storage Format

O Confluence usa XHTML como formato de armazenamento (Storage Format). Regras de conversão:

| Markdown | Confluence Storage Format |
|---|---|
| `# Título` | `<h1>Título</h1>` |
| `## Seção` | `<h2>Seção</h2>` |
| `**negrito**` | `<strong>negrito</strong>` |
| `_itálico_` | `<em>itálico</em>` |
| Tabela markdown | `<table><tbody><tr><th>...</th></tr><tr><td>...</td></tr></tbody></table>` |
| ` ```sql ... ``` ` | `<ac:structured-macro ac:name="code"><ac:parameter ac:name="language">sql</ac:parameter><ac:plain-text-body><![CDATA[...]]></ac:plain-text-body></ac:structured-macro>` |
| `[ ]` checkbox | `<ac:task-list><ac:task><ac:task-status>incomplete</ac:task-status><ac:task-body>texto</ac:task-body></ac:task></ac:task-list>` |
| `[X]` checkbox | `<ac:task-list><ac:task><ac:task-status>complete</ac:task-status><ac:task-body>texto</ac:task-body></ac:task></ac:task-list>` |
| Linha horizontal `---` | `<hr/>` |
| Bloco de nota/aviso | `<ac:structured-macro ac:name="info"><ac:rich-text-body><p>texto</p></ac:rich-text-body></ac:structured-macro>` |

**Regra geral:** todo o conteúdo do `value` deve ser XHTML válido. Tags devem estar corretamente fechadas.

---

## Tratamento de erros

| Código HTTP | Significado | Ação |
|---|---|---|
| 400 | Body inválido (XHTML mal formado) | Verificar conversão do markdown para storage format |
| 401 | Credenciais inválidas | Solicitar novo token ao DPO |
| 403 | Sem permissão no space | DPO deve verificar permissões no Confluence |
| 404 | Space ou página pai não encontrada | Confirmar `space_key` e `id` da página pai com o DPO |
| 409 | Conflito de versão (no PUT) | Buscar versão atual novamente e retentar |

Em caso de qualquer erro, **entregar o conteúdo em markdown** para o DPO publicar manualmente e informar:
> "Não foi possível publicar automaticamente. Erro: [código] — [descrição]. Segue o conteúdo formatado para publicação manual."

---

## Executar no Windows (PowerShell)

Exemplo de chamada via PowerShell:

```powershell
$url = "$env:CONFLUENCE_URL/wiki/rest/api/content"
$credentials = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes("$env:CONFLUENCE_EMAIL`:$env:CONFLUENCE_TOKEN"))

$headers = @{
    "Authorization" = "Basic $credentials"
    "Content-Type"  = "application/json"
}

$body = @{
    type  = "page"
    title = "DAPL-XXX — Nome do Produto"
    space = @{ key = "DADOS" }
    ancestors = @(@{ id = "123456" })
    body  = @{
        storage = @{
            value          = "<h1>Conteúdo aqui</h1>"
            representation = "storage"
        }
    }
} | ConvertTo-Json -Depth 10

$response = Invoke-RestMethod -Uri $url -Method Post -Headers $headers -Body $body
Write-Output "Página criada: $($response._links.base)$($response._links.webui)"
```
