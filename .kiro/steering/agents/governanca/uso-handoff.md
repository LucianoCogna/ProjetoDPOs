---
inclusion: manual
---

# Uso e Handoff — Agente de Governança | Documentação Confluence

## O que faz

Recebe o épico DAPL validado + DENA e gera o **documento de governança do produto de dados**, publicando automaticamente no Confluence via API.

**Output:** página criada no Confluence com todas as seções preenchidas a partir do DAPL e da DENA.

**NÃO faz:** preencher o histórico de versões (Seção 1.1), preencher metadados (Seção 6), inventar regras ou stakeholders.

---

## Pré-requisito

O **épico DAPL deve estar 100% validado** (sem nenhum `[?]` em aberto) antes de acionar o agente de governança. A DENA também deve estar completa, pois as regras de negócio e queries vêm de lá.

---

## Posição no fluxo geral

```
Refinamento:  DAPL → DENA → DDPL → DCOD
                                          ↓
Governança:                     Agente Governança → Confluence
Dev SQL:       Agente devs (DENA → SQL)
Dev Dataviz:   Agente dataviz (DCOD → HTML mockup)
```

O agente de governança é acionado **depois que o refinamento completo** — não precisa de um workspace específico, diferente do agente devs (que precisa do repo de catálogo).

---

## Como usar

### Fluxo padrão

1. Tenha o DAPL validado e a DENA completa em mãos.
2. Configure as credenciais do Confluence no ambiente (ou tenha em mãos para informar):
   - `CONFLUENCE_URL` — ex: `https://cogna.atlassian.net`
   - `CONFLUENCE_EMAIL` — seu e-mail Atlassian
   - `CONFLUENCE_TOKEN` — token de API (gerar em https://id.atlassian.com/manage-profile/security/api-tokens)
3. Ative o steering: **`#governanca-agent`**
4. Cole o DAPL e a DENA no chat.
5. O agente vai perguntar em **1 batch**:
   - Key Users (nome, área, e-mail, cargo)
   - Destino no Confluence (space, página pai, título)
6. Após sua resposta, o agente gera e publica automaticamente.
7. Você recebe o link da página criada + lista de pendências para completar depois.

### O que completar depois

Após a publicação automática, o DPO é responsável por:
- **Seção 1.1 — Histórico:** preencher manualmente no Confluence (data, versão, fase, autor)
- **Seção 6 — Metadados:** anexar a planilha de metadados na página
- **Campos `[? FALTA: ...]`:** revisar e preencher os campos que estavam ausentes no DAPL/DENA

---

## Como pedir ajuste no documento

Após a publicação, informe o que mudar:

> "Ajusta o objetivo estratégico para: [novo texto]"
> "Adiciona o key user: Nome, email, cargo"
> "Corrige o tipo de atualização para Full"

O agente atualiza a página no Confluence (nova versão) e confirma com o link.

---

## Diferença em relação aos outros agentes

| | agents/devs | agents/dataviz | agents/governanca |
|---|---|---|---|
| Insumo | DENA | DCOD | DAPL + DENA |
| Output | SQL (SELECT) | HTML mockup | Página no Confluence |
| Workspace necessário | Repo catálogo (datax-gcc) | Qualquer pasta | Qualquer pasta |
| Consulta ao repo? | Sim | Não | Não |
| Integração externa | Não | Não | Sim (Confluence API) |

---

## Manutenção dos arquivos de steering

Os 4 arquivos ficam em `.kiro/steering/agents/governanca/`:

- **Comportamento do agente** → `governanca-agent.md`
- **Template do documento** → `estrutura-documento.md`
- **Integração com Confluence** → `confluence-api.md`
- **Instruções de uso** → este arquivo (`uso-handoff.md`)

Após mudar qualquer arquivo, **reinicie o chat** para o Kiro reler o steering.
