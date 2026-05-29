# ETL_Ordem_Compra — Mapeamento Completo

## Visao Geral

| Aspecto | Valor |
|---|---|
| Servidor Origem | 169.57.181.231 (SOFTRAN_TRANSLUTE) |
| Servidor Destino | 10.100.86.89 (DBStage) |
| View Origem | "dbo"."vw_Ordens_Compras" |
| Tabela Destino | "dbo"."stg_Ordens_Compras" |
| Total de Colunas | 23 |
| Fluxos de Dados | 1 |

## Mapeamento Completo

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Grupo |
|---|---|---|---|---|
| 1 | EMISSAO | DT_DBTIMESTAMP | — | Data |
| 2 | FILIAL | DT_WSTR | 10 | Empresa |
| 3 | OC | DT_I4 | — | Identificacao |
| 4 | ITEM | DT_I4 | — | Identificacao |
| 5 | DESCRICAO | DT_WSTR | 80 | Produto |
| 6 | QTDE | DT_NUMERIC | 14,4 | Quantidade |
| 7 | VL_UNIT | DT_CY | — | Financeiro |
| 8 | VL_TOTAL | DT_CY | — | Financeiro |
| 9 | VlTotalRecebimento | DT_CY | — | Financeiro |
| 10 | DESC_NEGOC | DT_NUMERIC | 14,4 | Financeiro |
| 11 | ORCAMENTO | DT_I4 | — | Identificacao |
| 12 | COND_PGTO | DT_WSTR | 40 | Financeiro |
| 13 | FORNECDOR | DT_WSTR | 100 | Fornecedor |
| 14 | FRETE | DT_WSTR | 7 | Logistica |
| 15 | AUTORIZ_EM | DT_DBTIMESTAMP | — | Data |
| 16 | Usuario_Compra | DT_WSTR | 100 | Auditoria |
| 17 | AUTORIZ_POR | DT_WSTR | 10 | Auditoria |
| 18 | UsuarioBaixa | DT_WSTR | 50 | Auditoria |
| 19 | BAIXA | DT_DBTIMESTAMP | — | Data |
| 20 | NrContabilFiscal | DT_I4 | — | Fiscal |
| 21 | DsPlanoConta | DT_WSTR | 40 | Fiscal |
| 22 | CdTipoItem | DT_I4 | — | Classificacao |
| 23 | DsTipoItem | DT_WSTR | 30 | Classificacao |

## Distribuicao por Grupo

```
Data            [============================================================] 3 cols (13%)
Empresa         [====================] 1 col  (4%)
Identificacao   [============================================================] 3 cols (13%)
Produto         [====================] 1 col  (4%)
Quantidade      [====================] 1 col  (4%)
Financeiro      [========================================================================================================] 5 cols (22%)
Fornecedor      [====================] 1 col  (4%)
Logistica       [====================] 1 col  (4%)
Auditoria       [============================================================] 3 cols (13%)
Fiscal          [========================================] 2 cols (9%)
Classificacao   [========================================] 2 cols (9%)
```

## Totais por Tipo de Dado

| Tipo SSIS | Quantidade | Percentual |
|---|---|---|
| DT_WSTR (nvarchar/Unicode) | 10 | 43% |
| DT_I4 (integer) | 6 | 26% |
| DT_CY (currency/money) | 3 | 13% |
| DT_DBTIMESTAMP (datetime) | 3 | 13% |
| DT_NUMERIC | 2 | 9% |
| **Total** | **23** | **100%** |

> Unico package que usa DT_WSTR (Unicode). Os demais usam DT_STR (ANSI).
