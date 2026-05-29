# ETL_Performance_V2 — Mapeamento de Colunas

## Fluxo: ADO NET Source (vw_fPerformance) → ADO NET Destination (fPerformance)

Todas as 43 colunas são passadas diretamente (passthrough). Não há componentes de transformação.

| # | Coluna Origem (lineage)                                                     | Coluna Destino (fPerformance) | Tipo       | Comprimento/Precisão |
|---|-----------------------------------------------------------------------------|-------------------------------|------------|----------------------|
| 1 | ADO NET Source Output.Columns[NrDiasAtraso]                                 | NrDiasAtraso                  | i4         | —                    |
| 2 | ADO NET Source Output.Columns[Status]                                       | Status                        | wstr       | 14                   |
| 3 | ADO NET Source Output.Columns[DtPrevisaoEntregaCTeNova]                     | DtPrevisaoEntregaCTeNova      | dbTimeStamp| —                    |
| 4 | ADO NET Source Output.Columns[FichaViagem]                                  | FichaViagem                   | wstr       | 32                   |
| 5 | ADO NET Source Output.Columns[Hora]                                         | Hora                          | dbTimeStamp| —                    |
| 6 | ADO NET Source Output.Columns[IDCTEPERF]                                    | IDCTEPERF                     | wstr       | 42                   |
| 7 | ADO NET Source Output.Columns[CNPJRemetente]                                | CNPJRemetente                 | wstr       | 14                   |
| 8 | ADO NET Source Output.Columns[NomeRemetente]                                | NomeRemetente                 | wstr       | 100                  |
| 9 | ADO NET Source Output.Columns[NFSerie]                                      | NFSerie                       | wstr       | 3                    |
| 10 | ADO NET Source Output.Columns[NFNr]                                        | NFNr                          | i4         | —                    |
| 11 | ADO NET Source Output.Columns[NFVlr]                                       | NFVlr                         | numeric    | p=14, s=4            |
| 12 | ADO NET Source Output.Columns[NFDtEmissao]                                 | NFDtEmissao                   | dbTimeStamp| —                    |
| 13 | ADO NET Source Output.Columns[CTeEmpresa]                                  | CTeEmpresa                    | i4         | —                    |
| 14 | ADO NET Source Output.Columns[CTeNr]                                       | CTeNr                         | i4         | —                    |
| 15 | ADO NET Source Output.Columns[CTeDtEmissao]                                | CTeDtEmissao                  | dbTimeStamp| —                    |
| 16 | ADO NET Source Output.Columns[TipoDoctoFiscal]                             | TipoDoctoFiscal               | i4         | —                    |
| 17 | ADO NET Source Output.Columns[DtPrevisaoEntregaCTe]                        | DtPrevisaoEntregaCTe          | dbTimeStamp| —                    |
| 18 | ADO NET Source Output.Columns[DtAgendamentoEntregaCTe]                     | DtAgendamentoEntregaCTe       | dbTimeStamp| —                    |
| 19 | ADO NET Source Output.Columns[CTeDtEntrega]                                | CTeDtEntrega                  | dbTimeStamp| —                    |
| 20 | ADO NET Source Output.Columns[CNPJDestinatario]                            | CNPJDestinatario              | wstr       | 14                   |
| 21 | ADO NET Source Output.Columns[NomeDestinatario]                            | NomeDestinatario              | wstr       | 100                  |
| 22 | ADO NET Source Output.Columns[CidadeDestino]                               | CidadeDestino                 | wstr       | 30                   |
| 23 | ADO NET Source Output.Columns[UFDestino]                                   | UFDestino                     | wstr       | 2                    |
| 24 | ADO NET Source Output.Columns[CNPJPagador]                                 | CNPJPagador                   | wstr       | 14                   |
| 25 | ADO NET Source Output.Columns[NomePagador]                                 | NomePagador                   | wstr       | 100                  |
| 26 | ADO NET Source Output.Columns[NrSeqControle]                               | NrSeqControle                 | i4         | —                    |
| 27 | ADO NET Source Output.Columns[NaturezaCod]                                 | NaturezaCod                   | i4         | —                    |
| 28 | ADO NET Source Output.Columns[NaturezaNome]                                | NaturezaNome                  | wstr       | 40                   |
| 29 | ADO NET Source Output.Columns[TipoTransporte]                              | TipoTransporte                | wstr       | 40                   |
| 30 | ADO NET Source Output.Columns[Volumes]                                     | Volumes                       | numeric    | p=14, s=4            |
| 31 | ADO NET Source Output.Columns[QtPesoCubado]                                | QtPesoCubado                  | numeric    | p=14, s=4            |
| 32 | ADO NET Source Output.Columns[HistEntregaCod]                              | HistEntregaCod                | i4         | —                    |
| 33 | ADO NET Source Output.Columns[HistEntregaNome]                             | HistEntregaNome               | wstr       | 50                   |
| 34 | ADO NET Source Output.Columns[FichaVUltManifesto]                          | FichaVUltManifesto            | wstr       | 21                   |
| 35 | ADO NET Source Output.Columns[FichaVUltRom]                                | FichaVUltRom                  | wstr       | 32                   |
| 36 | ADO NET Source Output.Columns[DtRomaneio]                                  | DtRomaneio                    | dbTimeStamp| —                    |
| 37 | ADO NET Source Output.Columns[DtManifesto]                                 | DtManifesto                   | dbTimeStamp| —                    |
| 38 | ADO NET Source Output.Columns[TipoEmissao]                                 | TipoEmissao                   | wstr       | 16                   |
| 39 | ADO NET Source Output.Columns[DsGrupoCliente]                              | DsGrupoCliente                | wstr       | 100                  |
| 40 | ADO NET Source Output.Columns[CdGrupoCliente]                              | CdGrupoCliente                | i4         | —                    |
| 41 | ADO NET Source Output.Columns[DsRedespacho]                                | DsRedespacho                  | wstr       | 40                   |
| 42 | ADO NET Source Output.Columns[CdRedespacho]                                | CdRedespacho                  | wstr       | 14                   |
| 43 | ADO NET Source Output.Columns[NrRegistros]                                 | NrRegistros                   | i4         | —                    |

## Resumo por Tipo de Dado

| Tipo SSIS    | Quantidade de Colunas |
|--------------|-----------------------|
| wstr         | 21                    |
| i4 (int)     | 13                    |
| dbTimeStamp  | 9                     |
| numeric      | 3                     |
| **Total**    | **43**                |

## Caminho do Fluxo de Dados

```
ADO NET Source Output
    └── [path: ADO NET Source Output]
        └── ADO NET Destination Input (dbo.fPerformance)
```

Path SSIS: `Package\Sequence Container\Load fPerf.Paths[ADO NET Source Output]`
