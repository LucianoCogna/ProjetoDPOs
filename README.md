# ProjetoDPOs — Kiro Labs | Cogna Educação

Repositório de configuração do ambiente de trabalho do **Data Product Owner (DPO)** de Gente & Cultura na Cogna. Contém os steering files, hooks e contextos de demanda que alimentam o agente Kiro para suporte ao ciclo completo de refinamento de produtos de dados.

---

## O que é isso

O Kiro é um IDE com agente de IA integrado. Este repositório configura o comportamento desse agente para o contexto específico do DPO — desde a conversa exploratória até a geração de documentos formais no Jira (épicos, DENAs, DDPLs) e geração de SQL para engenharia.

O agente não substitui o DPO. Ele elimina o trabalho repetitivo: preencher templates, registrar decisões, atualizar Jira, manter contexto entre sessões.

---

## Estrutura do repositório

```
.kiro/
├── hooks/                          # Automações por evento no IDE
│   ├── sync-demanda-apos-produto.kiro.hook
│   └── ...
└── steering/                       # Instruções e contexto do agente
    ├── README.md                   # Setup técnico do Kiro
    ├── contexto-dpo.md             # Contexto geral do DPO (área, sistemas, backlog)
    ├── agents/
    │   ├── Refinamentos/           # Agente de refinamento de demandas
    │   │   ├── refinamento-agent.md    # Lógica de fluxo do agente
    │   │   ├── template-epico.md       # Template de épico (DAPL)
    │   │   ├── template-dena.md        # Template de card de engenharia (DENA)
    │   │   ├── template-ddpl.md        # Template de card de plataforma (DDPL)
    │   │   └── discovery-catalogo.md   # Fluxo de consulta ao repositório de catálogo
    │   └── devs/                   # Agente gerador de SQL
    │       ├── dev-agent.md            # Lógica do agente dev
    │       ├── padroes-sql-cogna.md    # Convenções SQL da casa (Athena/Redshift)
    │       ├── exemplos-validados.md   # Queries reais em produção como referência
    │       └── uso-handoff.md          # Instruções de uso para o DPO
    └── contexto/
        ├── projetos/               # Contexto estável por projeto/área
        │   ├── gec.md              # Gente & Cultura — sistemas, tabelas, linhagem
        │   └── esg.md              # ESG — indicadores, status, fontes
        ├── produtos/               # Contexto por produto de dados
        │   ├── treinamentos-unico.md   # Produto UniCo/Moodle (DAPL-543/550)
        │   └── gestao-desempenho.md    # Produto Elofy
        └── demandas/               # Contexto operacional por card Jira
            ├── dapl-550.md         # Treinamentos UniCo — estado atual, lacunas, próximos passos
            └── dapl-xxx-alianca.md # Template de demanda
```

---

## Como usar

### Pré-requisito

1. Ter o [Kiro](https://kiro.dev) instalado
2. Clonar este repositório
3. Abrir a pasta no Kiro — os steering files são detectados automaticamente

### Iniciando uma sessão

Chame os arquivos de contexto relevantes no chat com `#`:

```
#contexto-dpo          → carrega contexto geral da área (sistemas, backlog, glossário)
#gcc                   → carrega contexto do projeto Gente & Cultura
#treinamentos-unico    → carrega contexto do produto UniCo
#dapl-550              → carrega estado operacional da demanda DAPL-550
```

Sem chamar esses arquivos, o agente começa do zero. Com eles, retoma de onde parou.

### Fluxo de refinamento

```
1. Cole a transcrição da reunião ou notas no chat
2. O agente gera o épico (DAPL) preenchido com [?] nas lacunas
3. Decida se quer DENA (engenharia) ou DDPL (ingestão)
4. O agente preenche o card técnico com a query SQL estruturada
5. Copie o markdown direto para o Jira — ou peça ao agente para atualizar via integração
```

### Fluxo de geração de SQL

```
1. Abra também o repositório de catálogo (datax-gcc) no workspace
2. Cole o DENA no chat
3. O agente consulta as tabelas do repo, confirma engine e estratégia
4. Recebe o SELECT pronto para testar no Athena/Redshift
```

---

## Agentes disponíveis

### Agente de Refinamento (`agents/Refinamentos/`)

Suporta 3 modos de operação:

| Modo | Quando usar | O que faz |
|---|---|---|
| Conversa | Explorar uma demanda, pensar em voz alta | Responde, estrutura raciocínio, não gera docs |
| Discovery | Buscar tabelas/sistemas no catálogo | Consulta o repo e mapeia o que existe no Lake |
| Geração | Formalizar uma demanda | Gera épico → DENA → DDPL conforme pedido |

Documentos gerados:
- **Épico (DAPL):** necessidade de negócio, objetivo, escopo, critérios de aceite, origem dos dados
- **DENA:** regras de negócio, tabela de indicadores, estrutura da view, query SQL
- **DDPL:** metadados de ingestão (tabela origem, banco, destino, frequência)

### Agente Dev (`agents/devs/`)

Recebe um DENA e gera `SELECT` funcional pronto para testar. Suporta Athena (Presto/Trino) e Redshift. Consulta o repositório de catálogo para confirmar nomes reais de tabelas e colunas — nunca inventa.

---

## Hooks configurados

Automações que rodam em segundo plano no IDE:

| Hook | Evento | O que faz |
|---|---|---|
| `sync-demanda-apos-produto` | Salvar arquivo em `contexto/produtos/` | Verifica se o arquivo de demanda correspondente precisa ser atualizado |

---

## Contexto do domínio

**Área:** Gente & Cultura — Cogna Educação
**Domínio no Lake:** `gente_cultura`
**Repositório de catálogo:** [`datax-gcc`](https://github.com/LucianoCogna/datax-gcc) (não incluído aqui — tem seu próprio git)

**Sistemas ingeridos:** Capital Humano (RM), Blend, Elofy, Gupy, Service Now, UniCo/Moodle

**Engines:**
- `dp_gente_cultura_trusted` → Athena
- `business_gec` → Redshift
- `cdl_gente_business` → Athena ou Redshift

---

## Manutenção

| O que mudar | Onde mexer |
|---|---|
| Comportamento do agente de refinamento | `agents/Refinamentos/refinamento-agent.md` |
| Templates de épico/DENA/DDPL | `agents/Refinamentos/template-*.md` |
| Convenções SQL | `agents/devs/padroes-sql-cogna.md` |
| Adicionar query como referência | `agents/devs/exemplos-validados.md` |
| Contexto do projeto G&C | `contexto/projetos/gec.md` |
| Contexto de um produto específico | `contexto/produtos/[produto].md` |
| Estado operacional de uma demanda | `contexto/demandas/[dapl-xxx].md` |

Após qualquer mudança, reinicie o chat lateral no Kiro para recarregar os steering files.

Para subir as alterações:

```bash
git add .kiro/steering .kiro/hooks
git commit -m "feat: [descrição da mudança]"
git push
```

---

## Limitações conhecidas

- O agente não tem memória entre sessões — os arquivos de contexto em `contexto/demandas/` são a memória persistente. Mantenha-os atualizados.
- O agente não roda queries — gera o SQL e você testa no Athena/Redshift.
- Tabelas fora do repositório `datax-gcc` precisam ter o DDL colado manualmente no chat para o agente dev usar.
