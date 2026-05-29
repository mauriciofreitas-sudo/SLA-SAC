# Transformações — ETL_Abastecimento

## Resumo

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   ORIGEM (5 views/tabelas)        DESTINO (5 tabelas staging)    │
│   softran_translute  ──────────►  DBStage                        │
│                                                                  │
│              PASSTHROUGH DIRETO                                  │
│              Nenhuma transformação em nenhum dos 5 fluxos        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

O Data Flow Task **não contém nenhum componente de transformação** em nenhum dos 5 fluxos. Todos os dados são transferidos diretamente da origem para o destino:

- Sem `Derived Column`
- Sem `Data Conversion`
- Sem `Lookup`
- Sem `Conditional Split`
- Sem `Aggregate`
- Sem `Sort`
- Sem `Script Component`

---

## Componentes de Cada Fluxo

### Fluxo 1

```
[Origem OLE DB: vwrealabastecimento] ──────► [Destino OLE DB: stg_Abastecimento]
     169.57.181.231 / softran_translute           10.100.86.89 / DBStage
     27 colunas                                   27 colunas
```

### Fluxo 2

```
[Origem OLE DB: vwrealabastecimento2] ─────► [Destino OLE DB: stg_Abastecimento2]
     169.57.181.231 / softran_translute           10.100.86.89 / DBStage
     27 colunas                                   27 colunas
```

### Fluxo 3

```
[Origem OLE DB: Abasteciemtos ⚠️] ─────────► [Destino OLE DB: DescontoAbastecimento]
     169.57.181.231 / softran_translute           10.100.86.89 / DBStage
     5 colunas                                    5 colunas
```

### Fluxo 4

```
[Origem OLE DB: TipoVeiculo] ───────────────► [Destino OLE DB: Stg_PlacaAg_Combustivel]
     169.57.181.231 / softran_translute           10.100.86.89 / DBStage
     2 colunas                                    2 colunas
```

### Fluxo 5

```
[Origem OLE DB: vwRazaoVeiculo] ────────────► [Destino OLE DB: stg_RazaoVeiculo]
     169.57.181.231 / softran_translute           10.100.86.89 / DBStage
     14 colunas                                   14 colunas
```

---

## Pré-processamento (fora do Data Flow)

5 Execute SQL Tasks em paralelo removem todos os registros das tabelas staging antes da carga:

```sql
-- Executados em paralelo (ThreadHints 0–4):
DELETE FROM [dbo].[stg_Abastecimento]         -- ThreadHint 0
DELETE FROM [dbo].[stg_Abastecimento2]        -- ThreadHint 1
DELETE FROM [dbo].[stg_RazaoVeiculo]          -- ThreadHint 2
DELETE FROM [dbo].[Stg_PlacaAg_Combustivel]   -- ThreadHint 3
DELETE FROM [dbo].[DescontoAbastecimento]     -- ThreadHint 4
```

---

## Mapeamento de Tipos de Dados

| Tipo na Origem (OLE DB) | Tipo SSIS | Tipo esperado no Destino |
|---|---|---|
| `int` / `smallint` | `DT_I4` | `int` |
| `datetime` | `DT_DBTIMESTAMP` | `datetime` |
| `varchar(n)` codepage 1252 | `DT_STR(n)` | `varchar(n)` |
| `decimal(14,4)` / `numeric(14,4)` | `DT_NUMERIC(14,4)` | `decimal(14,4)` |
| `money` | `DT_CY` | `money` |

### Atenção — Tipos não convencionais

| Coluna | Tipo SSIS | Observação |
|---|---|---|
| `VLTotal`, `Quantidade`, `QtMediaApurada`, `ValorTotal` | `DT_CY` (Currency) | Mapeado para `money` no SQL Server — verifique se destino aceita `money` |
| `Dtemissao` (DescontoAbastecimento) | `DT_STR(30)` | **Data armazenada como texto** — formato não confirmado |
| `DtLancamento` (stg_RazaoVeiculo) | `DT_STR(30)` | **Data armazenada como texto** — formato não confirmado |
| `TIPO` (Stg_PlacaAg_Combustivel) | `DT_STR(1)` | Apenas 1 caractere: provável `A`, `P` ou `T` |

---

## Codepage

Todos os campos de texto usam `codePage="1252"` (Windows-1252 / Latin-1). O banco SOFTRAN usa collation non-Unicode — os dados são `varchar`, não `nvarchar`. Certifique-se de que a collation do DBStage seja compatível para evitar problemas de acentuação.
