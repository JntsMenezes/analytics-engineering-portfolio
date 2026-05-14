# Case 03 — Legal & Labor Process Intelligence

## Visão Geral

| Atributo | Detalhe |
|---|---|
| **Domínio** | Jurídico / Trabalhista / RH |
| **Tabelas** | 5 tabelas (Processos, Pedidos, Advogados, Adicionais, Calendário) |
| **Páginas** | 6 (Home, Gerencial, Analítico, Detalhe, Base) |
| **Ferramenta** | Power BI + SQL Server |

---

## Contexto

Dashboard de gestão de processos trabalhistas — domínio **extremamente raro em analytics**. A maioria das empresas não tem visibilidade analítica sobre seus passivos jurídicos. Decisões sobre contestar, acordar ou provisionar são tomadas caso a caso, sem benchmark histórico.

---

## Problema de Negócio

Departamento jurídico e RH sem visibilidade consolidada de:
- Custo real por processo (advogado + adicionais + condenação)
- Taxa de economia vs valor pedido pelo reclamante
- Padrões de processos por loja, função e tipo de reclamante
- Tempo médio de resolução por categoria

**Consequência:** provisões imprecisas, decisões reativas, impossibilidade de identificar lojas ou funções com maior incidência de reclamações.

---

## Modelo de Dados

```
PROCESSOS ──── PEDIDOS
     │
     ├──── ADVOGADOS
     │
     ├──── ADICIONAIS
     │
     └──── 1.CALENDARIO
```

### Tabelas

**`PROCESSOS`** — entidade central
- Número do processo judicial
- Loja, função do reclamante
- Status (pendente/finalizado)
- Risco classificado
- Tipo de reclamante

**`PEDIDOS`** — valores do processo
- Valor pedido pelo reclamante
- Custo final do processo
- % de economia vs pedido

**`ADVOGADOS`** — performance jurídica
- Custo por advogado
- Quantidade de processos
- Taxa de resolução

**`ADICIONAIS`** — custos extras
- Perícias, custas processuais, honorários adicionais

---

## KPIs Principais

| Métrica | Descrição |
|---|---|
| `% Custo vs Pedido` | Quanto do valor pedido foi efetivamente pago |
| `% Economia vs Pedido` | Economia obtida na negociação/sentença |
| `Médias Dias Processos` | Tempo médio de resolução |
| `Finalizados` | Processos encerrados no período |
| `Pendentes` | Processos em andamento |
| `CUSTO_PROCESSO` | Custo total consolidado |
| `DESPESA_ADVOGADO` | Honorários por advogado |

---

## Dimensões de Análise

- **Por Loja** — quais unidades geram mais processos
- **Por Função** — quais cargos têm maior incidência
- **Por Tipo de Reclamante** — perfil dos reclamantes
- **Por Advogado** — performance e custo do escritório jurídico
- **Por Risco** — classificação de risco do processo
- **Por Período** — sazonalidade e evolução temporal

---

## Diferenciais Técnicos

- **Domínio jurídico** — tradução de linguagem jurídica em modelo de dados analítico
- **Cruzamento RH × Jurídico** — lojas e funções com alta incidência viram input para RH
- **Análise de custo de advogado** — benchmark de performance de escritórios jurídicos
- **Risco classificado** — categorização por nível de risco auxilia provisão contábil

---

## Impacto de Negócio

- Redução de custos jurídicos através de decisões baseadas em dados históricos
- Identificação de padrões que permitem ação preventiva de RH antes de virar processo
- Suporte à decisão de contestar vs acordar com base em % histórico de economia
- Benchmark de escritórios jurídicos por custo e taxa de resolução
- Provisão mais precisa de passivo trabalhista para o financeiro

---

## Por que Este Case é Diferencial

Analytics jurídico é um nicho que praticamente nenhum analista de BI tem no portfólio. A capacidade de modelar dados de domínio jurídico — entendendo a relação entre processo, pedido, custo e advogado — demonstra habilidade de traduzir qualquer domínio de negócio em estrutura analítica.
