---
inclusion: manual
---

# Uso e Handoff — Agente Dataviz | Mockup Builder Power BI

## O que faz

Recebe um DCOD validado (ou uma descrição ad-hoc) e gera um **HTML interativo que simula visualmente um dashboard Power BI** — pronto para abrir no navegador e usar como contrato visual entre DPO e analista.

**Output:** arquivo HTML salvo em `output_mockup_builder/`, aberto automaticamente no navegador.

**NÃO faz:** arquivo `.pbix`, medidas DAX prontas para colar, conexão com fontes de dados reais.

---

## Diferença em relação ao agente devs (SQL)

| | agents/devs | agents/dataviz |
|---|---|---|
| Insumo principal | DENA validado | DCOD validado (ou ad-hoc) |
| Output | SELECT funcional (SQL) | HTML mockup interativo |
| Workspace necessário | Repo de catálogo (datax-gcc) | Qualquer pasta do projeto |
| Consulta ao repo? | Sim — confirma tabelas e colunas | Não — usa dados enviados pelo usuário |
| Persona | Ferramenta técnica direta | Guiado por perguntas (1 por vez) |

---

## Quando usar

- **Fluxo normal:** após o DCOD estar validado (sem `[?]` em aberto), o DPO pode ativar o agente dataviz para prototipar o dashboard antes do desenvolvimento.
- **Ad-hoc:** quando alguém precisa de um mockup rápido sem passar pelo fluxo formal de refinamento. Válido — não é obrigatório ter DCOD.

O mockup é **opcional** — nem todo DCOD precisa de protótipo. Use quando o solicitante precisa visualizar antes de aprovar, ou quando o layout é complexo e vale alinhar antes de desenvolver.

---

## Como usar — fluxo padrão

### Com DCOD

1. Tenha o DCOD validado em mãos (markdown do Jira ou do chat de refinamento).
2. Abra um novo chat e ative o steering: **`#dataviz-agent`**
3. Cole o DCOD quando o agente pedir.
4. Responda as 4 perguntas (objetivo, dados, tema visual, referência).
5. O agente gera o HTML, salva em `output_mockup_builder/` e abre no navegador.
6. Peça ajustes se necessário — o agente gera nova versão.
7. Passe o HTML aprovado para o analista como referência de desenvolvimento.

### Ad-hoc (sem DCOD)

1. Ative o steering: **`#dataviz-agent`**
2. Descreva o que precisa no chat (objetivo, público, dados disponíveis).
3. O agente faz as 4 perguntas e gera o mockup normalmente.

---

## Como enviar os dados

O agente aceita:
- Arquivo CSV, Excel ou TXT (arraste no chat)
- Tabela colada diretamente no chat
- Descrição das colunas disponíveis (se não tiver arquivo)

**Tamanho ideal:** 100–1000 linhas. Para bases maiores, envie uma amostra representativa.

O agente **nunca inventa dados**. Se a amostra for insuficiente para um visual, ele sinaliza no HTML com ⚠️ e explica o que falta no painel lateral.

---

## O que vem no HTML

- Dashboard visual em 1280 × 720 px (proporção Power BI)
- Dados reais da sua amostra nos gráficos
- Navegação entre páginas (se múltiplas páginas)
- Painel lateral retrátil com:
  - Parâmetros e filtros aplicados
  - Regra de negócio por visual
  - Colunas utilizadas
  - Sugestões de medidas DAX
  - Observações para o analista

---

## Como pedir ajuste

Depois que o HTML for gerado, basta descrever o que mudar:

> "Troca o gráfico de barras por um de linhas na página 2"
> "Adiciona filtro por regional no topo"
> "Muda o tema para Kroton"

O agente gera uma nova versão completa do HTML.

---

## Manutenção dos arquivos de steering

Os 4 arquivos ficam em `.kiro/steering/agents/dataviz/`:

- **Comportamento do agente** → `dataviz-agent.md`
- **Paletas de cores e temas** → `temas-cogna.md`
- **Limitações do Power BI** → `limitacoes-pbi.md`
- **Instruções de uso** → este arquivo (`uso-handoff.md`)

Após mudar qualquer arquivo, **reinicie o chat** para o Kiro reler o steering.
