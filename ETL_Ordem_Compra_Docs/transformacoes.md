# ETL_Ordem_Compra — Transformacoes

## Resumo

Este package e um **passthrough puro via ADO.NET**: nao ha transformacoes entre o source e o destino. A view `vw_Ordens_Compras` no Softran encapsula toda a logica de negocio (JOINs, calculos, etc.) e os dados chegam ja prontos para staging.

## Diagrama ASCII do Data Flow

```
[ADO NET Source]
  vw_Ordens_Compras
  (169.57.181.231 / SOFTRAN_TRANSLUTE)
  Timeout=600s
         |
         | 23 colunas passthrough
         |
[ADO NET Destination]
  INSERT → dbo.stg_Ordens_Compras
  (10.100.86.89 / DBStage)
  BulkInsert=true, Timeout=30s
```

## Pre-Processamento SQL

```sql
TRUNCATE TABLE [DBStage].[dbo].[stg_Ordens_Compras]
```

Mais eficiente que DELETE pois nao registra linha a linha no log de transacao.

## Componentes de Transformacao

**Nenhum.** Nao ha componentes de transformacao no pipeline.

## Mapeamento de Tipos

| # | Coluna | Tipo SSIS (Source) | Tipo External (Destino) | Discrepancia |
|---|---|---|---|---|
| 1 | EMISSAO | DT_DBTIMESTAMP | dbTimeStamp | OK |
| 2 | FILIAL | DT_WSTR(10) | wstr(10) | OK — Unicode |
| 3 | OC | DT_I4 | i4 | OK |
| 4 | ITEM | DT_I4 | i4 | OK |
| 5 | DESCRICAO | DT_WSTR(80) | wstr(80) | OK — Unicode |
| 6 | QTDE | DT_NUMERIC(14,4) | numeric(14,4) | OK |
| 7 | VL_UNIT | DT_CY | numeric(19,255)* | Escala 255 anomala |
| 8 | VL_TOTAL | DT_CY | numeric(19,255)* | Escala 255 anomala |
| 9 | VlTotalRecebimento | DT_CY | numeric(19,255)* | Escala 255 anomala |
| 10 | DESC_NEGOC | DT_NUMERIC(14,4) | numeric(14,4) | OK |
| 11 | ORCAMENTO | DT_I4 | i4 | OK |
| 12 | COND_PGTO | DT_WSTR(40) | wstr(40) | OK |
| 13 | FORNECDOR | DT_WSTR(100) | wstr(100) | OK (nome com typo) |
| 14 | FRETE | DT_WSTR(7) | wstr(7) | OK |
| 15 | AUTORIZ_EM | DT_DBTIMESTAMP | dbTimeStamp | OK |
| 16 | Usuario_Compra | DT_WSTR(100) | wstr(100) | OK |
| 17 | AUTORIZ_POR | DT_WSTR(10) | wstr(10) | OK |
| 18 | UsuarioBaixa | DT_WSTR(50) | wstr(50) | OK |
| 19 | BAIXA | DT_DBTIMESTAMP | dbTimeStamp | OK |
| 20 | NrContabilFiscal | DT_I4 | i4 | OK |
| 21 | DsPlanoConta | DT_WSTR(40) | wstr(40) | OK |
| 22 | CdTipoItem | DT_I4 | i4 | OK |
| 23 | DsTipoItem | DT_WSTR(30) | wstr(30) | OK |

(*) Metadata externa registrou scale=255 para campos monetarios — provavel artefato do ADO.NET provider ao mapear tipo `money`/`decimal` do SQL Server.

## Diferencas de Unicode

Este package usa **DT_WSTR** (Unicode) para strings, enquanto os outros tres packages usam **DT_STR** (ANSI/codepage 1252). Isso indica que a view `vw_Ordens_Compras` possui colunas nvarchar no SQL Server de origem.
