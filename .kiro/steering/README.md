---
inclusion: manual
---

# Agente de Refinamento DPO — Setup no Kiro

Esse pacote configura um agente especializado em refinamento de demandas de produtos de dados, rodando dentro do Kiro via steering files.

## O que tem aqui

```
.kiro/steering/
├── refinamento-agent.md   # System prompt do agente (lógica de fluxo)
├── template-epico.md      # Template de épico (sempre gerado)
├── template-dena.md       # Template de card de engenharia (opcional)
└── template-ddpl.md       # Template de card de plataforma (opcional)
```

Todos os arquivos têm `inclusion: always` no front-matter — ou seja, ficam **sempre carregados no contexto** do agente, em qualquer chat do Kiro nesse workspace.

## Como subir no Kiro

1. **Crie um workspace dedicado pro refinamento** (recomendado). Pode ser uma pasta nova só pra isso, sem código.
   ```
   refinamento-dpo/
   └── .kiro/
       └── steering/
   ```

2. **Cole os 4 arquivos `.md`** dentro de `.kiro/steering/`.

3. **Abra a pasta no Kiro.** Os steering files são detectados automaticamente.

4. **Verifique no chat lateral** — pergunte ao agente:
   > "Você tem os templates de épico, DENA e DDPL carregados?"
   
   Se ele confirmar listando os 3, está pronto.

## Como o time usa (fluxo padrão)

```
1. Abre o Kiro no workspace do refinamento
2. Abre o chat lateral
3. Cola transcrição/notas e diz "preciso refinar"
4. Recebe o épico preenchido + lista de [?]
5. Decide se quer DENA, DDPL, ou nenhum
6. Ajusta o que precisar
7. Copia o markdown direto pro Jira
```

Não tem botão, não tem comando especial. É só conversar.

## Próximos passos sugeridos

### Curto prazo (1-2 semanas)
- **Você usar por 5-10 refinamentos** sozinho antes de mostrar pro time
- Anotar onde o agente erra ou pergunta demais
- Iterar o `refinamento-agent.md` com base nos erros

### Médio prazo (1 mês)
- Mostrar para 1-2 DPOs de confiança
- Coletar feedback do output em comparação com o que eles fariam manualmente
- Refinar os exemplos few-shot nos templates

### Longo prazo (3+ meses)
- Padronizar como template oficial do time
- Avaliar integração com Jira via MCP (criar o card automaticamente, não só gerar markdown)
- Adicionar templates específicos por área (Captação, Gente, Financeiro) se fizer sentido

## Manutenção

Os 3 templates são **independentes**. Você pode editar `template-dena.md` sem afetar o épico. Versione no git — qualquer mudança fica registrada e você consegue voltar.

Se quiser mudar o comportamento do agente (mais conciso, mais perguntas, etc.), edite só o `refinamento-agent.md`. Os templates ficam estáveis.

## Limitações conhecidas

- **O agente não cria o card no Jira automaticamente.** Ele gera o markdown e você cola. Pra automatizar isso precisaria de integração via MCP (próximo passo).
- **Templates DENA/DDPL têm vieses do seu jeito de trabalhar.** Quando expandir pro time, esperar pequenos ajustes.
- **Performance depende da qualidade do input.** Transcrição muito ruim = mais `[?]`. É proposital — o agente é honesto sobre o que não sabe.
