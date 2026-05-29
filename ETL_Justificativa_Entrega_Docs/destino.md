# ETL_Justificativa_Entrega — Destino

## Atributos do Servidor de Destino

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |
| Provider | SQLOLEDB.1 |
| ConnectRetryCount | 1 |
| ConnectRetryInterval | 5s |
| ConnectionManager | DBSTACE |

## Destination Component: Justificativa_Entrega

| Atributo | Valor |
|---|---|
| Componente | Microsoft.OLEDBDestination |
| Tabela de Destino | [dbo].[stg_Justificativa_Entrega] |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | TABLOCK, CHECK_CONSTRAINTS |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadMaxInsertCommitSize | 2.147.483.647 (commit unico no final) |
| CommandTimeout | 0 (sem timeout) |

## Estrategia de Carga

**DELETE + INSERT (Full Refresh)**

1. A `Tarefa Executar SQL` executa `delete from [dbo].[stg_Justificativa_Entrega]` sem WHERE — deleta TODOS os registros.
2. O Data Flow insere todos os registros com FastLoad + TABLOCK.

## Colunas Mapeadas

| # | Coluna Destino | Tipo Destino | Tamanho | Mapeada de | Observacao |
|---|---|---|---|---|---|
| 1 | IDDOCUMENTO | str (DT_STR) | 70 | IDDOCUMENTO (42) | Destino tem 70, fonte 42 — margem OK |
| 2 | CdHistoricoEntrega | i4 (DT_I4) | — | CdHistoricoEntrega | |
| 3 | DsHistoricoEntrega | str (DT_STR) | 50 | DsHistoricoEntrega | |
| 4 | dsusuario | str (DT_STR) | 10 | dsusuario | |
| 5 | CdSequencia | i4 (DT_I4) | — | CdSequencia | |
| 6 | HrMovimento | dbTimeStamp | — | HrMovimento | |
| 7 | DtMovimento | dbTimeStamp | — | DtMovimento | |
| 8 | dtdigitacao | dbTimeStamp | — | dtdigitacao | |
| 9 | dsorigemmovto | str (DT_STR) | 30 | dsorigemmovto | |

## Configuracoes de Performance

| Configuracao | Valor | Impacto |
|---|---|---|
| AccessMode | FastLoad (3) | Bulk insert interno |
| TABLOCK | Ativado | Lock exclusivo na tabela durante carga |
| CHECK_CONSTRAINTS | Ativado | Validacao de constraints durante insert |
| Commit Size | 2.147.483.647 | Um unico commit ao final |
| CommandTimeout | 0 | Sem timeout (risco em tabelas grandes) |
