# ETL_Performance_V2 — Destino dos Dados

## Servidor de Destino

| Item              | Valor                                              |
|-------------------|----------------------------------------------------|
| Servidor          | 10.100.86.89                                       |
| Banco             | DBStage                                            |
| Usuário           | sqldba                                             |
| Connection Name   | 10.100.86.89.DBStage.sqldba 1 (ADO.NET)            |

## Estratégia de Carga

| Item                    | Detalhe                                                      |
|-------------------------|--------------------------------------------------------------|
| Estratégia              | Full Reload (DELETE + INSERT)                                |
| Pré-processamento       | `DELETE FROM dbo.fPerformance` (task "Truncate fPerf")       |
| Método de insert        | ADO.NET com SqlBulkCopy habilitado                           |
| BatchSize               | **50.000** linhas por lote                                   |
| CommandTimeout destino  | 30 segundos                                                  |
| validateExternalMetadata| False                                                        |

> Observação: A task "Truncate fPerf" usa `DELETE FROM` (sem WHERE) mas tem o parâmetro `User::StartDate` vinculado sem cláusula WHERE correspondente. Efetivamente apaga toda a tabela. Ver issues.

## Tabela de Destino: `dbo.fPerformance` (banco DBStage)

| # | Nome da Coluna              | Tipo Destino (External) | Precisão/Escala/Tamanho |
|---|-----------------------------|-------------------------|-------------------------|
| 1 | NrDiasAtraso                | i4                      | —                       |
| 2 | Status                      | wstr                    | 14                      |
| 3 | DtPrevisaoEntregaCTeNova    | dbTimeStamp             | —                       |
| 4 | FichaViagem                 | wstr                    | 32                      |
| 5 | Hora                        | dbTimeStamp             | —                       |
| 6 | IDCTEPERF                   | wstr                    | 42                      |
| 7 | CNPJRemetente               | wstr                    | 14                      |
| 8 | NomeRemetente               | wstr                    | 100                     |
| 9 | NFSerie                     | wstr                    | 3                       |
| 10 | NFNr                       | i4                      | —                       |
| 11 | NFVlr                      | numeric                 | p=14, s=4               |
| 12 | NFDtEmissao                | dbTimeStamp             | —                       |
| 13 | CTeEmpresa                 | i4                      | —                       |
| 14 | CTeNr                      | i4                      | —                       |
| 15 | CTeDtEmissao               | dbTimeStamp             | —                       |
| 16 | TipoDoctoFiscal            | i4                      | —                       |
| 17 | DtPrevisaoEntregaCTe       | dbTimeStamp             | —                       |
| 18 | DtAgendamentoEntregaCTe    | dbTimeStamp             | —                       |
| 19 | CTeDtEntrega               | dbTimeStamp             | —                       |
| 20 | CNPJDestinatario           | wstr                    | 14                      |
| 21 | NomeDestinatario           | wstr                    | 100                     |
| 22 | CidadeDestino              | wstr                    | 30                      |
| 23 | UFDestino                  | wstr                    | 2                       |
| 24 | CNPJPagador                | wstr                    | 14                      |
| 25 | NomePagador                | wstr                    | 100                     |
| 26 | NrSeqControle              | i4                      | —                       |
| 27 | NaturezaCod                | i4                      | —                       |
| 28 | NaturezaNome               | wstr                    | 40                      |
| 29 | TipoTransporte             | wstr                    | 40                      |
| 30 | Volumes                    | numeric                 | p=14, s=4               |
| 31 | QtPesoCubado               | numeric                 | p=14, s=4               |
| 32 | HistEntregaCod             | i4                      | —                       |
| 33 | HistEntregaNome            | wstr                    | 50                      |
| 34 | FichaVUltManifesto         | wstr                    | 21                      |
| 35 | FichaVUltRom               | wstr                    | 32                      |
| 36 | DtRomaneio                 | dbTimeStamp             | —                       |
| 37 | DtManifesto                | dbTimeStamp             | —                       |
| 38 | TipoEmissao                | wstr                    | 16                      |
| 39 | DsGrupoCliente             | wstr                    | 100                     |
| 40 | CdGrupoCliente             | i4                      | —                       |
| 41 | DsRedespacho               | wstr                    | 40                      |
| 42 | CdRedespacho               | wstr                    | 14                      |
| 43 | NrRegistros                | i4                      | —                       |

**Total: 43 colunas**

## Pré-processamento SQL (Task "Truncate fPerf")

```sql
DELETE FROM dbo.fPerformance
```

- Conexão: 10.100.86.89.DBStage.sqldba (ADO.NET)
- ThreadHint: 0
- Parâmetro `User::StartDate` vinculado ao índice 0, mas sem WHERE na query
- Efetivamente equivale a DELETE sem filtro (apaga tudo)
