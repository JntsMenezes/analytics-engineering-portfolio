# Case 01 — CRM Analytics & Governance Audit

## Visão Geral

| Atributo | Detalhe |
|---|---|
| **Domínio** | CRM / Fidelidade / Varejo Farma |
| **Volume** | ~1.5GB de dados comprimidos |
| **Tabelas** | 10 no modelo + 19 LocalDateTables ocultas |
| **Medidas DAX** | 30+ distribuídas em 3 tabelas |
| **Ferramenta** | Power BI (Import Mode) + SQL Server |
| **Metodologia** | Auditoria arquitetural via pbi-tools + TMDL |

---

## Contexto

Modelo semântico de CRM recebido sem documentação, sem contexto e sem padrão definido. O objetivo foi realizar uma **auditoria arquitetural completa** antes de qualquer evolução — identificando riscos de segurança, problemas de performance e oportunidades de melhoria estrutural.

Ferramenta utilizada para extração: **pbi-tools** (extração do modelo em formato TMDL legível).

---

## Achados Críticos de Segurança

### 1. Coluna `senha` carregada no modelo

A query da tabela de clientes utilizava `SELECT A.*`, trazendo a coluna de senha para dentro do arquivo `.pbix`.

```sql
-- PROBLEMA
SELECT A.* FROM dbo.clientes A WITH(NOLOCK)

-- CORREÇÃO
SELECT
    A.id,
    A.cpf,
    A.nome,
    A.email,
    A.genero,
    A.data_nascimento,
    A.situacao,
    A.origem_cadastro,
    A.criado_em
FROM dbo.clientes A WITH(NOLOCK)
```

**Impacto:** qualquer pessoa com acesso ao arquivo `.pbix` consegue extrair os hashes de senha via Export Data ou pbi-tools.

---

### 2. PII sem Row-Level Security

Campos sensíveis em Import Mode sem RLS configurado:
- `cpf`
- `email`
- `celular`
- `data_nascimento`
- `token_lembrar_me`

---

### 3. CPF real em comentário SQL

```sql
-- ENCONTRADO em produção (dado fictício para demonstração)
-- WHERE CPF = '000.000.000-00'
```

Dado pessoal hardcoded num comentário dentro do código de uma query de produção.

---

## Achados de Performance

### 4. 19 LocalDateTables ocultas

A opção **Auto date/time** estava ativa. Cada coluna do tipo `datetime` gerou sua própria tabela de calendário oculta — 19 ao total.

**Origem das 19 tabelas:**
- `Calendario`: 7 colunas datetime → 7 LocalDateTables
- `Clientes`: 6 colunas datetime → 6 LocalDateTables
- `PRODUTO`: 1 → 1
- `D_CLIENTES`: 1 → 1
- `CUPONS_IDENTIFICADOS`: 1 → 1
- `RangeStart` / `RangeEnd`: 2 → 2 LocalDateTables

**Correção:** desativar Auto date/time em `File → Options → Data Load → Auto date/time`.

---

### 5. Tabela calculada com função legada `EARLIER`

```dax
-- PROBLEMA: TBL_MOVIMENTO_SECOES como tabela calculada materializada
TBL_MOVIMENTO_SECOES =
SUMMARIZE(
    CUPONS_IDENTIFICADOS,
    CUPONS_IDENTIFICADOS[MOVIMENTO],
    "MediaSecoes",
        AVERAGEX(
            SUMMARIZE(
                FILTER(
                    CUPONS_IDENTIFICADOS,
                    CUPONS_IDENTIFICADOS[MOVIMENTO] = EARLIER(CUPONS_IDENTIFICADOS[MOVIMENTO])
                ), ...
            ), [QtdSecoes]
        )
)

-- CORREÇÃO: converter em medida calculada sob demanda
[Categorias por Cupom] :=
VAR Resumo =
    SUMMARIZE(
        CUPONS_IDENTIFICADOS,
        CUPONS_IDENTIFICADOS[CUPOM_CHAVE],
        "QtdSecoes", DISTINCTCOUNT(CUPONS_IDENTIFICADOS[SECAO_PRODUTO])
    )
RETURN AVERAGEX(Resumo, [QtdSecoes])
```

---

### 6. Data hardcoded no Calendário

```sql
-- PROBLEMA
WHERE dt BETWEEN '01-01-2025' AND GETDATE()

-- CORREÇÃO: usar parâmetros dinâmicos
WHERE dt BETWEEN DATEADD(YEAR, -3, GETDATE()) AND GETDATE()
```

Qualquer análise YoY ou comparativo com ano anterior retornava em branco.

---

## Achados de Arquitetura

### 7. Lógica de gênero com typo silencioso

```sql
-- PROBLEMA: 'i' minúsculo em 'FEMINiNO'
WHEN ISNULL(B.genero, F.SEXO) = 'FEMINiNO' THEN 'F'

-- RESULTADO: todos os registros femininos do sistema legado retornam NULL
-- IMPACTO: análises de gênero incorretas há meses, sem erro visível
```

---

### 8. VAR MAXDT com lógica frágil

```dax
-- PROBLEMA: repedido em 4 medidas
VAR MAXDT = MAX(Calendario[Dt]) - 1

-- Com slicer de data ativo, MAX retorna a última data visível,
-- não o dia anterior ao atual. Resultado muda conforme contexto do visual.

-- CORREÇÃO
VAR MAXDT = TODAY() - 1
```

---

### 9. Medidas órfãs em produção

```dax
measure Medida      -- completamente vazia
measure 'Medida 2'  -- completamente vazia
```

---

### 10. Naming convention com 4 padrões misturados

| Contexto | Problema |
|---|---|
| Tabelas | `Clientes` (PascalCase) vs `PRODUTO` (UPPER) vs `D_CLIENTES` (prefixo D_) vs `TBL_MOVIMENTO_SECOES` (prefixo TBL_) |
| Colunas | `cpf` (minúsculo) em `Clientes` vs `CPF` (maiúsculo) em `D_CLIENTES` |
| Medidas | `Faturamento Clube` vs `FATURAMENTO IDENTIFICADOS CLUBE` — mesmo KPI, lógicas diferentes |

---

## Mapa de Prioridades

| Prioridade | Ação | Esforço | Impacto |
|---|---|---|---|
| P0 | Remover `SELECT *` e coluna `senha` | Baixo | **Crítico** |
| P0 | Desativar Auto date/time (19 LocalDateTables) | Baixo | Alto |
| P1 | Unificar `D_CLIENTES` e `Clientes` | Médio | Alto |
| P1 | Conectar `RangeStart`/`RangeEnd` às queries SQL | Médio | Alto |
| P1 | Converter `TBL_MOVIMENTO_SECOES` em medida | Baixo | Médio |
| P1 | Corrigir typo `FEMINiNO` na query SQL | Baixo | Alto |
| P2 | Centralizar medidas em tabela `_Medidas` | Médio | Médio |
| P2 | Substituir `SELECT *` em `PRODUTO` | Baixo | Médio |
| P2 | Criar `D_LOJA` e `D_VENDEDOR` | Alto | Alto |
| P3 | Padronizar naming convention | Alto | Baixo |
| P3 | Migrar para PBIP + Git com pbi-tools | Alto | Alto (longo prazo) |

---

## Arquitetura Proposta

```
                    Dim_Data
                    Dim_Cliente
                    Dim_Produto
                    Dim_Perfil
                    Dim_Loja
                    Dim_Vendedor
                         ↓
              Fato_CuponsIdentificados
              Fato_HistoricoPerfil
```

---

## Aprendizados

1. `SELECT *` em tabelas de usuário é risco de segurança — sempre selecionar colunas explicitamente
2. Auto date/time deve ser desativado em todo modelo enterprise
3. `EARLIER` é legado — substituir por `VAR` em qualquer lógica nova
4. Typos silenciosos em SQL (`FEMINiNO`) não geram erro — geram dado errado
5. Datas hardcoded em queries de produção impedem análise histórica
6. Medidas devem ter camadas: `_Base → Contextuais → KPIs`

---

## Ferramentas Utilizadas

- **pbi-tools** — extração do modelo `.pbix` em formato TMDL
- **DAX Studio** — análise de medidas e performance
- **Git** — versionamento do modelo extraído
- **Power BI Desktop** — validação das correções
