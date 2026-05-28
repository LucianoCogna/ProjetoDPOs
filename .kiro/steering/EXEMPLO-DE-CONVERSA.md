---
inclusion: manual
---

# Exemplo de conversa esperada com o agente

> Este arquivo serve só pra você ver como o agente vai se comportar, antes de testar de verdade.
> Não precisa ir pro `.kiro/steering/` — é só documentação de referência.

---

## Turno 1 — DPO abre o chat

**DPO:**
> oi, preciso refinar uma demanda

**Agente:**
> Pronto pra refinar. Me manda as informações — pode ser transcrição da reunião, notas, links, o que tiver. Eu monto o épico primeiro.

---

## Turno 2 — DPO cola transcrição bagunçada

**DPO:**
> Reunião com a Carla da Captação ontem. Eles tão sofrendo pra acompanhar funil de inscrição, aprovação e matrícula porque hoje é planilha manual. Quer um painel pra ver por polo. Dados já tão no Lake (Olimpo e Colaborar). PBI mesmo. Não tem prazo definido mas é prioridade alta porque tá perto do fechamento do ciclo. Histórico desde 2023. Polos EAD ela não falou se entra ou não.

**Agente:**

```markdown
# Criar produto de dados de Funil de Captação por Polo

**ID:** DAPL-XXXX  
**Prioridade:** Alta  
**Tipo de demanda:** 🆕 Novo Produto de Dados  
**Camadas envolvidas:** Trusted, Business, Visualização (PBI)  

---

## 1. Necessidade de negócio

### Contexto
A área de Captação acompanha hoje o funil de inscrição, aprovação e matrícula via planilhas manuais. Esse processo gera atraso e retrabalho na consolidação por polo.

### Problema ou oportunidade
Sem visão consolidada e tempestiva, a área não identifica gargalos a tempo de agir antes do fechamento do ciclo de matrícula.

### Quem é impactado
- **Área solicitante:** Captação de Alunos
- **Stakeholders secundários:** [? FALTA: stakeholders secundários não foram citados]

---

## 2. Objetivo do épico

Como analista de Captação,  
Quero um painel consolidado de funil (inscrição → aprovação → matrícula) por polo,  
Para que eu identifique gargalos antes do fechamento do ciclo e atue em tempo hábil.

---

## 3. Tipo de demanda

- [X] 🆕 Novo Produto de Dados
- [-] 📊 Visualização / DataViz

---

## 4. Camadas envolvidas

- [X] Trusted (dp_trusted)
- [X] Business (cdl_*_business)
- [X] Visualização (PBI)

---

## 5. Escopo

### Dentro do escopo
- View consolidada de funil (inscrição, aprovação, matrícula) por polo
- Dashboard PBI com filtro por polo e ciclo
- Histórico desde 2023

### Fora do escopo
- [? FALTA: Polos EAD não foram definidos — entram ou não nesta versão?]

### Premissas
- Dados de inscrição vêm do Olimpo (já no Lake)
- Dados de matrícula vêm do Colaborar (já no Lake)

### Restrições conhecidas
- [? FALTA: prazo não definido, mas há urgência do fechamento de ciclo]

---

## 6. Critérios de aceite

- [ ] View do funil disponível na camada business
- [ ] Dashboard PBI publicado em workspace da Captação
- [ ] Documentação no Dicionário de Dados
- [ ] Homologação pela Carla (Captação)

---

## 7. Origem dos dados

- **Fonte(s):** Olimpo (inscrições), Colaborar (matrículas/aprovações)
- **Tipo:** Interno
- **Frequência de atualização:** [? proposta: Diária — confirmar]
- **Formato de entrega:** Banco de dados (já no Lake)
- **Contato técnico da fonte:** [? FALTA: não identificado]

---

## ⚠️ Pontos que precisam de validação

1. **Escopo — Polos EAD:** Carla não definiu se entram. *Sugestão: confirmar antes do início do desenvolvimento.*
2. **Restrições — Prazo:** sem prazo formal, mas há janela de fechamento de ciclo. *Sugestão: alinhar uma data-limite com a área.*
3. **Stakeholders secundários:** apenas Carla foi citada. *Sugestão: identificar Diretoria/equipes regionais que também consomem o painel.*
4. **Frequência:** Diária é proposta padrão — confirmar com Carla.
5. **Contato técnico:** identificar com o time de Eng. de Dados.
```

> Quer que eu gere também:
> - **DENA** (card de engenharia) — pra criação da view + modelagem
> - **DDPL** (card de plataforma) — não parece necessário, dados já estão no Lake
>
> Pode pedir um, os dois, ou nenhum.

---

## Turno 3 — DPO pede o DENA

**DPO:**
> sim, faz o DENA

**Agente:**
> Antes de gerar, preciso de 3 infos técnicas que não estavam claras:
>
> 1. **Schema destino da view:** `cdl_captacao_business` é o correto? Ou tem outro nome?
> 2. **Nome sugerido da view:** `vw_funil_captacao_polo` parece razoável — confirma?
> 3. **Granularidade do histórico:** desde 2023, mas é por mês? Por ciclo? Por dia?

---

## Turno 4 — DPO responde

**DPO:**
> 1. sim, cdl_captacao_business
> 2. confirma
> 3. por dia mesmo, e a gente filtra por ciclo no PBI

**Agente:**

```markdown
# [DENA] Criar view de Funil de Captação por Polo

**Épico vinculado:** DAPL-XXXX  
**Camada destino:** cdl_captacao_business  
**Frequência de atualização:** [? proposta: D-1 — confirmar]  

---

## Descrição

Desenvolver view consolidada de funil de captação (inscrição → aprovação → matrícula) por polo, materializada em `cdl_captacao_business.vw_funil_captacao_polo`. Origem dos dados: Olimpo (inscrições) e Colaborar (aprovações/matrículas), já presentes no Lake.

---

## Regras de negócio

- Granularidade: 1 linha por polo + dia + etapa do funil
- Histórico: desde 01/01/2023
- Etapas do funil: inscrição, aprovação, matrícula (nessa ordem cronológica)
- [? FALTA: regra de deduplicação de inscrições do mesmo aluno no mesmo polo]

---

## Pontos importantes

- **Origem:** Olimpo (inscrições), Colaborar (aprovações/matrículas)
- **Destino:** `cdl_captacao_business.vw_funil_captacao_polo`
- **Frequência:** [? D-1 proposto]
- **Escopo de dados:** todas as inscrições/matrículas desde 01/01/2023
- **Cruzamentos necessários:** Olimpo × Colaborar por chave de aluno
- **Polos EAD:** [? FALTA: definir inclusão antes do desenvolvimento]

---

## Indicadores

| Frente | Indicador | Resumo | Quebras / Variações |
|---|---|---|---|
| Captação | Inscritos | Total de alunos inscritos | Polo, ciclo, curso, data |
| Captação | Aprovados | Total de alunos aprovados na seleção | Polo, ciclo, curso, data |
| Captação | Matriculados | Total de alunos com matrícula efetivada | Polo, ciclo, curso, data |
| Captação | Taxa de conversão (inscrição→matrícula) | % de inscritos que se matricularam | Polo, ciclo, curso |
| Captação | Funil completo | Visão linha-a-linha de inscrição→aprovação→matrícula por aluno | Polo, ciclo, curso |

### Filtros gerais aplicáveis
- Polo
- Ciclo de captação
- Curso
- Faixa de data

---

## Exemplo de estrutura da view final

```sql
-- Nome: vw_funil_captacao_polo
-- Schema: cdl_captacao_business
```

| Coluna | Tipo | Descrição |
|---|---|---|
| polo_id | varchar | Identificador do polo |
| polo_nome | varchar | Nome do polo |
| ciclo | varchar | Ciclo de captação (ex: 2025.1) |
| curso_id | varchar | Identificador do curso |
| curso_nome | varchar | Nome do curso |
| data_evento | date | Data do evento (inscrição/aprovação/matrícula) |
| qtd_inscritos | int | Quantidade de inscritos |
| qtd_aprovados | int | Quantidade de aprovados |
| qtd_matriculados | int | Quantidade de matriculados |
| [? incluir flag EAD?] | boolean | A definir |

---

## Critérios de aceite técnicos

- [ ] View `vw_funil_captacao_polo` materializada em `cdl_captacao_business`
- [ ] Atualização D-1 rodando estável
- [ ] Conferência amostral contra Olimpo e Colaborar
- [ ] Histórico desde 2023 carregado
- [ ] Documentação técnica no repositório

---

## ⚠️ Pontos que precisam de validação

1. **Frequência D-1:** proposto, falta confirmação formal.
2. **Polos EAD:** decisão pendente do épico.
3. **Regra de deduplicação:** o que fazer se mesmo aluno se inscrever 2x no mesmo polo/ciclo?
```

---

## Comportamentos esperados (e o que validar)

### ✅ Comportamentos corretos
- Agente é **breve no cumprimento**
- Preenche tudo que dá pra preencher
- Marca `[?]` honestamente nas lacunas
- Lista pontos de validação ao final
- Oferece DENA/DDPL sem forçar
- Faz batch de perguntas (não 1 por 1)
- Output em markdown pronto pra copy-paste

### ❌ Comportamentos que devem ser corrigidos (ajustar o agent prompt)
- Adicionar disclaimers tipo "este é um draft inicial"
- Inventar prazos, números ou stakeholders
- Fazer perguntas 1 por 1
- Repetir contexto entre seções
- Usar linguagem de marketing
- Gerar DENA/DDPL sem ser pedido
