# ETL_Romaneios — Mapeamento de Colunas

## Fluxo: ADO NET Source (Ficha_de_Viagem_Geral) → ADO NET Destination (fFichaViagem)

Todas as 51 colunas são passadas diretamente (passthrough). Não há componentes de transformação intermediários.

| # | Coluna Origem (lineage)                                              | Coluna Destino (fFichaViagem) | Tipo        | Comprimento/Precisão |
|---|----------------------------------------------------------------------|-------------------------------|-------------|----------------------|
| 1 | ADO NET Source Output.Columns[EmpresaFicha]                          | EmpresaFicha                  | i4          | —                    |
| 2 | ADO NET Source Output.Columns[NrFicha]                               | NrFicha                       | i4          | —                    |
| 3 | ADO NET Source Output.Columns[DtEmissao]                             | DtEmissao                     | dbTimeStamp | —                    |
| 4 | ADO NET Source Output.Columns[VlComissao]                            | VlComissao                    | numeric     | p=14, s=4            |
| 5 | ADO NET Source Output.Columns[CdRotaRMS]                             | CdRotaRMS                     | i4          | —                    |
| 6 | ADO NET Source Output.Columns[MotoristaFV]                           | MotoristaFV                   | wstr        | 100                  |
| 7 | ADO NET Source Output.Columns[ProprietarioFV]                        | ProprietarioFV                | wstr        | 100                  |
| 8 | ADO NET Source Output.Columns[VeiculoFV]                             | VeiculoFV                     | wstr        | 8                    |
| 9 | ADO NET Source Output.Columns[CarretaFV]                             | CarretaFV                     | wstr        | 8                    |
| 10 | ADO NET Source Output.Columns[Carreta2FV]                           | Carreta2FV                    | wstr        | 8                    |
| 11 | ADO NET Source Output.Columns[Carreta3FV]                           | Carreta3FV                    | wstr        | 8                    |
| 12 | ADO NET Source Output.Columns[UserFicha]                            | UserFicha                     | wstr        | 10                   |
| 13 | ADO NET Source Output.Columns[EmpresaRomaneio]                      | EmpresaRomaneio               | i4          | —                    |
| 14 | ADO NET Source Output.Columns[RotaRomaneio]                         | RotaRomaneio                  | i4          | —                    |
| 15 | ADO NET Source Output.Columns[RomaneioManifesto]                    | RomaneioManifesto             | wstr        | 20                   |
| 16 | ADO NET Source Output.Columns[UserRomaneioManifesto]                | UserRomaneioManifesto         | wstr        | 10                   |
| 17 | ADO NET Source Output.Columns[DtEmissaoRmMan]                       | DtEmissaoRmMan                | dbTimeStamp | —                    |
| 18 | ADO NET Source Output.Columns[VeiculoRmMan]                         | VeiculoRmMan                  | wstr        | 8                    |
| 19 | ADO NET Source Output.Columns[CarretaRmMan]                         | CarretaRmMan                  | wstr        | 8                    |
| 20 | ADO NET Source Output.Columns[Carreta2RmMan]                        | Carreta2RmMan                 | wstr        | 8                    |
| 21 | ADO NET Source Output.Columns[Carreta3RmMan]                        | Carreta3RmMan                 | wstr        | 8                    |
| 22 | ADO NET Source Output.Columns[MotoristaRmMan]                       | MotoristaRmMan                | wstr        | 40                   |
| 23 | ADO NET Source Output.Columns[ProprietarioRmMan]                    | ProprietarioRmMan             | wstr        | 40                   |
| 24 | ADO NET Source Output.Columns[NumeroCTRB]                           | NumeroCTRB                    | i4          | —                    |
| 25 | ADO NET Source Output.Columns[UserCtrb]                             | UserCtrb                      | wstr        | 10                   |
| 26 | ADO NET Source Output.Columns[DtEmissaoCTRB]                        | DtEmissaoCTRB                 | dbTimeStamp | —                    |
| 27 | ADO NET Source Output.Columns[ValorTotalCTRB]                       | ValorTotalCTRB                | numeric     | p=14, s=4            |
| 28 | ADO NET Source Output.Columns[AdtoCTRB]                             | AdtoCTRB                      | numeric     | p=14, s=4            |
| 29 | ADO NET Source Output.Columns[PedagioCTRB]                          | PedagioCTRB                   | numeric     | p=14, s=4            |
| 30 | ADO NET Source Output.Columns[EmpresaCTE]                           | EmpresaCTE                    | i4          | —                    |
| 31 | ADO NET Source Output.Columns[VlFretePeso]                          | VlFretePeso                   | numeric     | p=14, s=4            |
| 32 | ADO NET Source Output.Columns[VlFreteValor]                         | VlFreteValor                  | numeric     | p=14, s=4            |
| 33 | ADO NET Source Output.Columns[VlPedagio]                            | VlPedagio                     | numeric     | p=14, s=4            |
| 34 | ADO NET Source Output.Columns[VlGRIS]                               | VlGRIS                        | numeric     | p=14, s=4            |
| 35 | ADO NET Source Output.Columns[VlTotalPrestacao]                     | VlTotalPrestacao              | numeric     | p=14, s=4            |
| 36 | ADO NET Source Output.Columns[NrDoctoFiscal]                        | NrDoctoFiscal                 | i4          | —                    |
| 37 | ADO NET Source Output.Columns[UserCTE]                              | UserCTE                       | wstr        | 10                   |
| 38 | ADO NET Source Output.Columns[TipoDoctoFiscal]                      | TipoDoctoFiscal               | wstr        | 10                   |
| 39 | ADO NET Source Output.Columns[DtEmissaoCTE]                         | DtEmissaoCTE                  | dbTimeStamp | —                    |
| 40 | ADO NET Source Output.Columns[CidadeColeta]                         | CidadeColeta                  | wstr        | 30                   |
| 41 | ADO NET Source Output.Columns[DsUFOrigem]                           | DsUFOrigem                    | wstr        | 2                    |
| 42 | ADO NET Source Output.Columns[CidadeEntrega]                        | CidadeEntrega                 | wstr        | 30                   |
| 43 | ADO NET Source Output.Columns[DsUFDestino]                          | DsUFDestino                   | wstr        | 2                    |
| 44 | ADO NET Source Output.Columns[CdRemetente]                          | CdRemetente                   | wstr        | 14                   |
| 45 | ADO NET Source Output.Columns[DsRemetente]                          | DsRemetente                   | wstr        | 100                  |
| 46 | ADO NET Source Output.Columns[CdDestinatario]                       | CdDestinatario                | wstr        | 14                   |
| 47 | ADO NET Source Output.Columns[DsDestinatario]                       | DsDestinatario                | wstr        | 100                  |
| 48 | ADO NET Source Output.Columns[CdInscricao]                          | CdInscricao                   | wstr        | 14                   |
| 49 | ADO NET Source Output.Columns[DsPagador]                            | DsPagador                     | wstr        | 100                  |
| 50 | ADO NET Source Output.Columns[CdRedespacho]                         | CdRedespacho                  | wstr        | 14                   |
| 51 | ADO NET Source Output.Columns[DsRedespacho]                         | DsRedespacho                  | wstr        | 40                   |

## Resumo por Tipo de Dado

| Tipo SSIS    | Quantidade de Colunas |
|--------------|-----------------------|
| wstr         | 34                    |
| numeric      | 9                     |
| i4 (int)     | 4                     |
| dbTimeStamp  | 4                     |
| **Total**    | **51**                |

## Caminho do Fluxo de Dados

```
ADO NET Source Output
    └── [path: ADO NET Source Output]
        └── ADO NET Destination Input (dbo.fFichaViagem)
```

Path SSIS: `Package\Load fFicha.Paths[ADO NET Source Output]`
