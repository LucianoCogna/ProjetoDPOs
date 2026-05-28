---
inclusion: always
---

# Template — DDPL | Card de Plataforma / Ingestão

> **Quando usar:** quando o épico envolve **ingestão de nova fonte/tabela** no Data Lake, ou configuração de plataforma.
> **Output destino:** card no projeto DDPL do Jira.
> **Estilo:** curto, técnico, baseado em metadados. Sem narrativa longa.

---

## Estrutura obrigatória do output

```markdown
# [DDPL] Ingestão — [Nome da tabela ou fonte]

**Épico vinculado:** DAPL-XXXX  
**Tipo:** [Ingestão nova | Atualização de ingestão | Configuração de ambiente]

---

## Descrição

Realizar a ingestão da tabela [nome] do sistema [origem] para o Data Lake, conforme as configurações abaixo.

[1-2 frases adicionais SE houver contexto relevante de plataforma. Caso contrário, deixe só a frase acima.]

---

## Tabela(s) origem

- `[SCHEMA].[NOME_TABELA]`
- [adicionar mais linhas se houver múltiplas tabelas]

---

## Banco de dados origem

- **Tipo:** [Estruturado | Não estruturado | Semi-estruturado]
- **Caminho / Servidor:** `[endereço completo do servidor]`
- **Database:** `[NOME_DO_DB]`
- **Credencial de acesso:** [secret manager / vault / contato]

---

## Destino no Data Lake

- **Camada:** [dp_*_trusted | dp_*_raw | dp_*_business]
- **Schema destino:** `[nome do schema]`
- **Tabela renomeada como:** `[novo_nome_da_tabela]`
- **Frequência de ingestão:** [D-1 | semanal | mensal | sob demanda]

---

## Critérios de aceite

- [ ] Tabela ingerida na camada acordada com o nome especificado
- [ ] Ingestão rodando na frequência definida
- [ ] Schema da tabela destino documentado
- [ ] Conferência de volumetria origem vs destino em amostragem
- [ ] [? outros critérios específicos]
```

---

## Instruções de preenchimento (para o agente)

### Filosofia do DDPL

Este card é **curto e técnico**. Não escreva parágrafos longos. Não inclua justificativa de negócio (isso está no épico). Foco total em **metadados de ingestão**.

### Informações obrigatórias

Se faltar **qualquer um** destes, faça 1 batch de perguntas antes de gerar:

1. Nome completo da tabela origem (`[SCHEMA].[TABELA]`)
2. Endereço do servidor / caminho de banco
3. Database de origem
4. Camada de destino no Lake
5. Nome da tabela no destino (padrão Cogna costuma ser `[prefixo]_[origem]_[descricao]_v[N]`)
6. Frequência de ingestão

### Padrões de nomenclatura observados

- Tabelas de histórico: prefixo `hst_`
- Versionamento: sufixo `_v1`, `_v2` etc.
- Prefixos de sistema variam por domínio (ex: `_gc_`, `_fin_`, `_cap_`) — confirme no repo

Se a transcrição não mencionar o nome final, **proponha** um seguindo os padrões observados no repo e marque `[? proposta — confirmar]`.

### Múltiplas tabelas no mesmo card

Se a demanda envolve ingestão de várias tabelas relacionadas (ex: 3 tabelas de histórico do mesmo sistema), liste todas na seção "Tabela(s) origem" em vez de criar 3 DDPLs separados. Avise o DPO no final:

> "Agrupei as N tabelas neste DDPL único. Se preferir um card por tabela, me avisa."

---

## Exemplo de output bem preenchido

> **Use este exemplo como referência de concisão e estrutura — não como modelo de conteúdo.**
> Os dados abaixo são ilustrativos (domínio RH). Substitua pelos dados reais da demanda.

```markdown
# [DDPL] Ingestão — [Nome da tabela ou fonte]

**Épico vinculado:** DAPL-XXXX  
**Tipo:** Ingestão nova

---

## Descrição

Realizar a ingestão da tabela [nome] do sistema [origem] para o Data Lake.

---

## Tabela(s) origem

- `[SCHEMA_ORIGEM].[NOME_TABELA]`

---

## Banco de dados origem

- **Tipo:** Estruturado
- **Caminho / Servidor:** `[endereço do servidor]`
- **Database:** `[NOME_DO_DB]`
- **Credencial de acesso:** [? FALTA: indicar secret/contato]

---

## Destino no Data Lake

- **Camada:** [dp_*_trusted | dp_*_raw]
- **Schema destino:** `[nome_do_schema_destino]`
- **Tabela renomeada como:** `[prefixo_sistema_descricao_v1]`
- **Frequência de ingestão:** [? FALTA: confirmar D-1 ou outra]

---

## Critérios de aceite

- [ ] Tabela disponível no schema destino com o nome especificado
- [ ] Ingestão rodando na frequência definida
- [ ] Conferência de volumetria origem vs destino

---

## ⚠️ Pontos que precisam de validação

1. **Credencial:** confirmar onde está armazenada a credencial de acesso ao banco origem.
2. **Frequência:** não foi mencionada na demanda — confirmar com o solicitante.
```
