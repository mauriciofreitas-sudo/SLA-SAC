# ETL_Ordem_Compra — Destino

## Atributos do Servidor de Destino

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage (via conexao "DWGrupolc") |
| Usuario | sqldba |
| Provider | ADO.NET (System.Data.SqlClient) |
| ConnectionManager | 10.100.86.89.DWGrupolc.sqldba |

## Destination Component: ADO NET Destination

| Atributo | Valor |
|---|---|
| Componente | Microsoft.ManagedComponentHost (ADONETDestination) |
| Tabela de Destino | "dbo"."stg_Ordens_Compras" |
| BatchSize | 0 (usa buffer interno do SSIS) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true |

## Estrategia de Carga

**TRUNCATE + INSERT (Full Refresh)**

1. `TRUNCATE TABLE [DBStage].[dbo].[stg_Ordens_Compras]` — muito mais rapido que DELETE pois nao registra cada linha no log.
2. INSERT via ADO.NET BulkInsert.

## Colunas Mapeadas

| # | Coluna Destino | Tipo SSIS | Tamanho/Precisao | Tipo no External |
|---|---|---|---|---|
| 1 | EMISSAO | DT_DBTIMESTAMP | — | dbTimeStamp |
| 2 | FILIAL | DT_WSTR | 10 | wstr(10) |
| 3 | OC | DT_I4 | — | i4 |
| 4 | ITEM | DT_I4 | — | i4 |
| 5 | DESCRICAO | DT_WSTR | 80 | wstr(80) |
| 6 | QTDE | DT_NUMERIC | 14,4 | numeric(14,4) |
| 7 | VL_UNIT | DT_CY | — | numeric(19,255)* |
| 8 | VL_TOTAL | DT_CY | — | numeric(19,255)* |
| 9 | VlTotalRecebimento | DT_CY | — | numeric(19,255)* |
| 10 | DESC_NEGOC | DT_NUMERIC | 14,4 | numeric(14,4) |
| 11 | ORCAMENTO | DT_I4 | — | i4 |
| 12 | COND_PGTO | DT_WSTR | 40 | wstr(40) |
| 13 | FORNECDOR | DT_WSTR | 100 | wstr(100) |
| 14 | FRETE | DT_WSTR | 7 | wstr(7) |
| 15 | AUTORIZ_EM | DT_DBTIMESTAMP | — | dbTimeStamp |
| 16 | Usuario_Compra | DT_WSTR | 100 | wstr(100) |
| 17 | AUTORIZ_POR | DT_WSTR | 10 | wstr(10) |
| 18 | UsuarioBaixa | DT_WSTR | 50 | wstr(50) |
| 19 | BAIXA | DT_DBTIMESTAMP | — | dbTimeStamp |
| 20 | NrContabilFiscal | DT_I4 | — | i4 |
| 21 | DsPlanoConta | DT_WSTR | 40 | wstr(40) |
| 22 | CdTipoItem | DT_I4 | — | i4 |
| 23 | DsTipoItem | DT_WSTR | 30 | wstr(30) |

(*) Discrepancia: Source produz DT_CY (currency), External metadata registrou numeric(19,255) — escala 255 e anomala.

## Configuracoes de Performance

| Configuracao | Valor |
|---|---|
| UseBulkInsert | true |
| BatchSize | 0 (buffer SSIS) |
| CommandTimeout | 30s |
| Sem TABLOCK explicito | ADO.NET usa BulkCopy (lock por default menos agressivo) |
