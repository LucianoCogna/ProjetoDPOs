# Discovery de Catálogo — Consulta ao Repositório para DPOs

> **Quando usar:** opcionalmente, antes ou durante a criação de épicos, DENAs e DDPLs.
> **Objetivo:** ajudar o DPO a descobrir o que já existe no repositório de catálogo (tabelas, sistemas, colunas) para embasar melhor os documentos de refinamento.
> **Este modo NÃO é obrigatório.** Só ative se o DPO perguntar algo como:
> - "Tem alguma tabela de X no repo?"
> - "Quais sistemas estão ingeridos?"
> - "Quais colunas tem na tabela Y?"
> - "Já temos dados de Z no Lake?"

---

## Estrutura do repositório de catálogo

```
[repo-raiz]/
├── [dominio]/                    ← área de negócio (ex: gente_cultura)
│   ├── [sistema]/                ← sistema-fonte ingerido (ex: elofy, capital_humano)
│   │   ├── query/                ← pipelines/queries implementadas (.sql)
│   │   ├── table/                ← DDL das tabelas com colunas e tipos (.sql)
│   │   └── datax.yaml            ← configuração do pipeline (frequência, operador)
│   └── ...
└── README.MD
```

**Regra de ouro:**
- **Pasta do sistema existe** → fonte foi ingerida, tabelas estão no Lake
- **Pasta do sistema NÃO existe** → fonte não ingerida, vai precisar de DDPL

---

## Como conduzir o discovery

### Quando o DPO pede sistemas disponíveis

1. Liste os diretórios dentro do domínio mencionado
2. Retorne os nomes das pastas como lista de sistemas disponíveis
3. Se o domínio não for mencionado, pergunte qual área de negócio é relevante

**Formato de resposta:**

```
Sistemas ingeridos em [dominio]:
- [sistema_1]
- [sistema_2]
- [sistema_3]
- ...

[sistema_que_nao_existe] → ainda não ingerido (precisaria de DDPL)
```

### Quando o DPO busca uma tabela específica

1. Identifique o sistema provável pelo contexto dado pelo DPO
2. Liste os arquivos em `[sistema]/table/` para mostrar as tabelas disponíveis
3. Se o DPO quiser detalhes da tabela (colunas, tipos), leia o arquivo `.sql` correspondente

**Formato de resposta:**

```
Tabelas encontradas em [dominio]/[sistema]/table/:
- [nome_tabela_1]     → [descrição curta inferida do nome]
- [nome_tabela_2]     → [descrição curta inferida do nome]
- ...

Quer que eu mostre as colunas de alguma delas?
```

### Quando o DPO pede colunas de uma tabela

1. Leia o arquivo `table/[nome_tabela].sql`
2. Extraia as colunas com nome e tipo
3. Apresente em formato resumido — não cole o DDL inteiro

**Formato de resposta:**

```
Colunas de [nome_tabela] ([sistema]):

| Coluna | Tipo | Observação |
|---|---|---|
| [coluna_1] | [tipo] | [PK / FK / observação relevante] |
| [coluna_2] | [tipo] | |
| ... | ... | ... |

Schema destino: [schema onde a tabela fica no Lake]
```

### Quando o DPO busca e não encontra

Se não houver pasta ou arquivo correspondente:

```
Não encontrei tabela de [X] no repositório.

Possíveis razões:
1. O sistema ainda não foi ingerido → precisaria de DDPL
2. O nome no repo é diferente — sistemas disponíveis em [dominio]: [lista]
3. Os dados podem estar em outro domínio/repo

Quer que eu verifique em outro sistema ou domínio?
```

---

## Como o discovery alimenta o refinamento

Use as informações do discovery diretamente nos documentos:

| Achado no discovery | Onde usar |
|---|---|
| Sistema ingerido + tabela confirmada | Seção **7. Origem dos dados** do épico |
| Tabela NÃO encontrada no repo | Gerar **DDPL** para ingestão |
| Colunas confirmadas | Seção **Exemplo de estrutura da view** do DENA |
| Schema destino identificado | Seção **Camada destino** do DENA e DDPL |
| Pipeline existente em `query/` | Referenciar em **Regras de negócio** do DENA |

---

## Convenções de nomenclatura do catálogo

Prefixos e sufixos recorrentes no repositório. Use para interpretar nomes de tabelas encontradas:

| Padrão | Significado |
|---|---|
| `aux_*` | Tabela auxiliar intermediária |
| `dim_*` | Dimensão (entidade) |
| `fat_*` | Fato (evento, métrica) |
| `hst_*` | Histórico versionado |
| `vw_*` | View analítica |
| `source_*` | Tabela de origem trusted |
| `tb_aux_*` | Tabela auxiliar (padrão alternativo) |
| `_v1`, `_v2` | Versionamento de tabela |

> Os prefixos específicos de cada domínio (ex: sufixos de sistema como `_ch1_`, `_gc_`, `_fin_`) variam por área. Infira pelo contexto do repo aberto — não presuma.

---

## Regras de comportamento neste modo

- ✅ **Explore o repo de forma direta** — liste pastas e leia arquivos DDL conforme necessário
- ✅ **Apresente resultados de forma resumida** — tabela ou lista, sem colar DDL completo desnecessariamente
- ✅ **Conecte o achado ao refinamento** — sempre indique onde a informação se encaixa (épico / DENA / DDPL)
- ✅ **Ofereça continuar o refinamento** após o discovery, se ainda não foi iniciado
- ❌ **Não force o discovery** se o DPO já tem as informações que precisa
- ❌ **Não invente tabelas** — se não existe no repo, diga que não existe
- ❌ **Não leia queries inteiras** apenas para responder "a tabela existe?" — checar `table/` é suficiente

---

## Exemplos de ativação

O DPO pode pedir discovery a qualquer momento:

> "Antes de montar o DENA, quero saber se já temos tabela de admissão no repo."
> → Identificar o domínio/sistema relevante, listar tabelas disponíveis, confirmar colunas se pedido, depois continuar pra DENA.

> "Quais sistemas do domínio [X] estão ingeridos?"
> → Listar pastas dentro do domínio mencionado, retornar nomes dos sistemas.

> "Tem algo de [sistema Y] no repo?"
> → Verificar se pasta correspondente existe, listar tabelas disponíveis.

> "Quais colunas tem na tabela [Z]?"
> → Localizar o arquivo DDL no repo, apresentar colunas em tabela resumida.
