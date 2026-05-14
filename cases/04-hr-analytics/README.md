# Case 04 — People Analytics & Workforce Intelligence

## Visão Geral

| Atributo | Detalhe |
|---|---|
| **Domínio** | RH / Pessoas / Gestão de Workforce |
| **Tabelas** | 8 tabelas |
| **Páginas** | 10 (Home + Headcount, Turnover, Absenteísmo, Folha, PCD) |
| **Ferramenta** | Power BI + SQL Server |

---

## Contexto

Suite completa de People Analytics cobrindo os principais indicadores de gestão de pessoas: Headcount, Turnover, Absenteísmo, Folha de Pagamento e compliance com cotas de PCD e Jovem Aprendiz.

Diferencial: **comparativo Orçado vs Realizado** em Headcount — integração entre RH e planejamento financeiro.

---

## Problema de Negócio

Área de RH sem visibilidade analítica consolidada:
- Headcount calculado manualmente em planilhas, sem histórico
- Turnover sem rastreabilidade por motivo de desligamento
- Absenteísmo sem classificação por tipo ou área
- Nenhum comparativo entre quadro planejado e realizado
- Cotas de PCD e Jovem Aprendiz monitoradas manualmente

---

## Modelo de Dados

### Tabelas

| Tabela | Tipo | Descrição |
|---|---|---|
| `HEADCOUNT` | Fato | Registros de colaboradores ativos/desligados |
| `HEADCOUNT_ORCADO` | Fato | Quadro planejado por centro de custo |
| `CARGOS` | Dimensão | Hierarquia de cargos e funções |
| `CENTROS_CUSTOS` | Dimensão | Estrutura organizacional |
| `D_CALENDARIO` | Dimensão | Calendário de datas |
| `SUPERVISAO` | Dimensão | Estrutura de supervisão regional |
| `CONSULTA_PCD` | Suporte | Base de colaboradores PCD |
| `CONSULTA_JOVEM_AP` | Suporte | Base de jovens aprendizes |

---

## KPIs por Módulo

### Headcount
```dax
[#HEADCOUNT_REAL]         -- colaboradores ativos no período
[#HeadCount Crescimento]  -- variação ano a ano
[#Var]                    -- diferença orçado vs realizado
[HEADCOUNT_ORCADO]        -- quadro planejado
```

### Turnover
- Taxa de turnover por período
- Desligamentos por motivo
- Comparativo por unidade de negócio e cargo

### Absenteísmo
- Índice de absenteísmo
- Classificação por tipo de ausência
- Análise por área e cargo

### Compliance
- Percentual de PCD vs cota legal
- Percentual de Jovem Aprendiz vs cota legal
- Alertas de risco de descumprimento

---

## Dimensões de Análise

- **Por Cargo** — headcount e turnover por função
- **Por Centro de Custo** — custo com pessoal por área
- **Por Unidade de Negócio** — visão consolidada por empresa/regional
- **Por Empresa Contábil / Usuária** — separação de entidades jurídicas
- **Por Período** — evolução mensal e anual

---

## Diferenciais Técnicos

- **Orçado vs Realizado** — headcount planejado no budget vs quadro real, por centro de custo
- **Multi-empresa** — modelo suporta múltiplas empresas contábeis e usuárias no mesmo ambiente
- **Compliance automático** — cálculo automático de aderência às cotas de PCD e Jovem Aprendiz
- **10 páginas estruturadas** — cada módulo de RH com sua própria visão analítica

---

## Impacto de Negócio

- Eliminação de planilhas manuais de headcount
- Visibilidade imediata de desvio entre quadro planejado e realizado
- Identificação de áreas com alto turnover para ação preventiva
- Suporte ao DP para monitoramento de cotas legais
- Dados consolidados para reuniões de resultado de RH
