# ETL_Painel_CTE — Mapeamento de Colunas

## Fluxo: ADO NET Source → ADO NET Destination (dbo.fBaseCTE)

Todas as 74 colunas são passadas diretamente (passthrough). Não há componentes de transformação intermediários no Data Flow.

| # | Coluna Origem (lineage)                                           | Coluna Destino (fBaseCTE)   | Tipo Origem  | Tipo Destino       |
|---|-------------------------------------------------------------------|-----------------------------|--------------|--------------------|
| 1 | ADO NET Source Output.Columns[cdempresa]                          | cdempresa                   | wstr(50)     | wstr(50)           |
| 2 | ADO NET Source Output.Columns[DsApelidoEmitente]                  | DsApelidoEmitente           | wstr(3)      | wstr(3)            |
| 3 | ADO NET Source Output.Columns[CEPColeta]                          | CEPColeta                   | i4           | i4                 |
| 4 | ADO NET Source Output.Columns[CEPDestino]                         | CEPDestino                  | i4           | i4                 |
| 5 | ADO NET Source Output.Columns[dsplacaveiculo]                     | dsplacaveiculo              | wstr(8)      | wstr(8)            |
| 6 | ADO NET Source Output.Columns[NrPlacaReboque1]                    | NrPlacaReboque1             | wstr(8)      | wstr(8)            |
| 7 | ADO NET Source Output.Columns[DsMotorista]                        | DsMotorista                 | wstr(40)     | wstr(40)           |
| 8 | ADO NET Source Output.Columns[VlFretePeso]                        | VlFretePeso                 | cy           | numeric p=19 s=255 |
| 9 | ADO NET Source Output.Columns[VlFreteValor]                       | VlFreteValor                | cy           | numeric p=19 s=255 |
| 10 | ADO NET Source Output.Columns[VlPedagio]                         | VlPedagio                   | cy           | numeric p=19 s=255 |
| 11 | ADO NET Source Output.Columns[VlGRIS]                            | VlGRIS                      | cy           | numeric p=19 s=255 |
| 12 | ADO NET Source Output.Columns[VlTotalPrestacao]                  | VlTotalPrestacao            | cy           | numeric p=19 s=255 |
| 13 | ADO NET Source Output.Columns[nrdoctofiscal]                     | nrdoctofiscal               | i4           | i4                 |
| 14 | ADO NET Source Output.Columns[CdTpDoctoFiscal]                   | CdTpDoctoFiscal             | i4           | i4                 |
| 15 | ADO NET Source Output.Columns[dsapelido]                         | dsapelido                   | wstr(10)     | wstr(10)           |
| 16 | ADO NET Source Output.Columns[DtEmissao]                         | DtEmissao                   | wstr(30)     | wstr(30)           |
| 17 | ADO NET Source Output.Columns[DsSituacao]                        | DsSituacao                  | wstr(9)      | wstr(9)            |
| 18 | ADO NET Source Output.Columns[DsTpFrete]                         | DsTpFrete                   | wstr(1)      | wstr(1)            |
| 19 | ADO NET Source Output.Columns[DsTipoEmissao]                     | DsTipoEmissao               | wstr(16)     | wstr(16)           |
| 20 | ADO NET Source Output.Columns[DsUFOrigem]                        | DsUFOrigem                  | wstr(2)      | wstr(2)            |
| 21 | ADO NET Source Output.Columns[DsUFDestino]                       | DsUFDestino                 | wstr(2)      | wstr(2)            |
| 22 | ADO NET Source Output.Columns[dtentrega]                         | dtentrega                   | wstr(30)     | wstr(30)           |
| 23 | ADO NET Source Output.Columns[DtEmissaoFatura]                   | DtEmissaoFatura             | wstr(30)     | wstr(30)           |
| 24 | ADO NET Source Output.Columns[dtvencimento]                      | dtvencimento                | wstr(30)     | wstr(30)           |
| 25 | ADO NET Source Output.Columns[QtPeso]                            | QtPeso                      | cy           | numeric p=19 s=255 |
| 26 | ADO NET Source Output.Columns[QtMetrosCubicos]                   | QtMetrosCubicos             | cy           | numeric p=19 s=255 |
| 27 | ADO NET Source Output.Columns[VlMercadoria]                      | VlMercadoria                | cy           | numeric p=19 s=255 |
| 28 | ADO NET Source Output.Columns[VlLiquido]                         | VlLiquido                   | cy           | numeric p=19 s=255 |
| 29 | ADO NET Source Output.Columns[VlICMS]                            | VlICMS                      | cy           | numeric p=19 s=255 |
| 30 | ADO NET Source Output.Columns[QtVolume]                          | QtVolume                    | cy           | numeric p=19 s=255 |
| 31 | ADO NET Source Output.Columns[CdRemetente]                       | CdRemetente                 | wstr(14)     | wstr(14)           |
| 32 | ADO NET Source Output.Columns[DsRemetente]                       | DsRemetente                 | wstr(100)    | wstr(100)          |
| 33 | ADO NET Source Output.Columns[CdDestinatario]                    | CdDestinatario              | wstr(14)     | wstr(14)           |
| 34 | ADO NET Source Output.Columns[dsDestinatario]                    | dsDestinatario              | wstr(100)    | wstr(100)          |
| 35 | ADO NET Source Output.Columns[CdConsignatario]                   | CdConsignatario             | wstr(14)     | wstr(14)           |
| 36 | ADO NET Source Output.Columns[dsConsignatario]                   | dsConsignatario             | wstr(100)    | wstr(100)          |
| 37 | ADO NET Source Output.Columns[CdInscricao]                       | CdInscricao                 | wstr(14)     | wstr(14)           |
| 38 | ADO NET Source Output.Columns[dsPagador]                         | dsPagador                   | wstr(100)    | wstr(100)          |
| 39 | ADO NET Source Output.Columns[CdEmpresaDestino]                  | CdEmpresaDestino            | i4           | i4                 |
| 40 | ADO NET Source Output.Columns[DsEmpDest]                         | DsEmpDest                   | wstr(10)     | wstr(10)           |
| 41 | ADO NET Source Output.Columns[CidadeColeta]                      | CidadeColeta                | wstr(30)     | wstr(30)           |
| 42 | ADO NET Source Output.Columns[CidadeEntrega]                     | CidadeEntrega               | wstr(30)     | wstr(30)           |
| 43 | ADO NET Source Output.Columns[cdfatura]                          | cdfatura                    | i4           | i4                 |
| 44 | ADO NET Source Output.Columns[InTDE]                             | InTDE                       | wstr(3)      | wstr(3)            |
| 45 | ADO NET Source Output.Columns[DoctoOrigem]                       | DoctoOrigem                 | i4           | i4                 |
| 46 | ADO NET Source Output.Columns[NrNotaFiscal]                      | NrNotaFiscal                | nText (MAX)  | wstr MAX           |
| 47 | ADO NET Source Output.Columns[DsSituacaoArq]                     | DsSituacaoArq               | wstr(13)     | wstr(13)           |
| 48 | ADO NET Source Output.Columns[VlFreteOrigem]                     | VlFreteOrigem               | numeric p14s4 | numeric p=14 s=4  |
| 49 | ADO NET Source Output.Columns[VlICMSOrigem]                      | VlICMSOrigem                | numeric p14s4 | numeric p=14 s=4  |
| 50 | ADO NET Source Output.Columns[dsnatureza]                        | dsnatureza                  | wstr(40)     | wstr(40)           |
| 51 | ADO NET Source Output.Columns[dstransporte]                      | dstransporte                | wstr(40)     | wstr(40)           |
| 52 | ADO NET Source Output.Columns[DsUltimaOcorrencia]                | DsUltimaOcorrencia          | wstr(50)     | wstr(50)           |
| 53 | ADO NET Source Output.Columns[DtUltimaOcorrencia]                | DtUltimaOcorrencia          | wstr(30)     | wstr(30)           |
| 54 | ADO NET Source Output.Columns[DsUsuario]                         | DsUsuario                   | wstr(10)     | wstr(10)           |
| 55 | ADO NET Source Output.Columns[NomeMotorista]                     | NomeMotorista               | wstr(40)     | wstr(40)           |
| 56 | ADO NET Source Output.Columns[NrRomaneio]                        | NrRomaneio                  | wstr(16)     | wstr(16)           |
| 57 | ADO NET Source Output.Columns[NrPlacaRomaneio]                   | NrPlacaRomaneio             | wstr(8)      | wstr(8)            |
| 58 | ADO NET Source Output.Columns[PlacaManifInterior]                | PlacaManifInterior          | wstr(8)      | wstr(8)            |
| 59 | ADO NET Source Output.Columns[DtSaidaParceiro]                   | DtSaidaParceiro             | dbTimeStamp  | dbTimeStamp        |
| 60 | ADO NET Source Output.Columns[DsItinerario]                      | DsItinerario                | wstr(7)      | wstr(7)            |
| 61 | ADO NET Source Output.Columns[ArquivoConemb]                     | ArquivoConemb               | wstr(255)    | wstr(255)          |
| 62 | ADO NET Source Output.Columns[DtEnvioConemb]                     | DtEnvioConemb               | dbTimeStamp  | dbTimeStamp        |
| 63 | ADO NET Source Output.Columns[ArquivoOcoren]                     | ArquivoOcoren               | wstr(255)    | wstr(255)          |
| 64 | ADO NET Source Output.Columns[DtEnvioOcoren]                     | DtEnvioOcoren               | dbTimeStamp  | dbTimeStamp        |
| 65 | ADO NET Source Output.Columns[ArquivoDocCob]                     | ArquivoDocCob               | wstr(255)    | wstr(255)          |
| 66 | ADO NET Source Output.Columns[DtEnvioDocCob]                     | DtEnvioDocCob               | dbTimeStamp  | dbTimeStamp        |
| 67 | ADO NET Source Output.Columns[Pedido]                            | Pedido                      | wstr(30)     | wstr(30)           |
| 68 | ADO NET Source Output.Columns[Conteudo]                          | Conteudo                    | wstr(111)    | wstr(111)          |
| 69 | ADO NET Source Output.Columns[Previsão de Entrega]               | Previsão de Entrega         | wstr(30)     | wstr(30)           |
| 70 | ADO NET Source Output.Columns[Dt Inclusao Ocorrência]            | Dt Inclusao Ocorrência      | wstr(61)     | wstr(61)           |
| 71 | ADO NET Source Output.Columns[Dt Ocorrencia]                     | Dt Ocorrencia               | wstr(61)     | wstr(61)           |
| 72 | ADO NET Source Output.Columns[Descrição Ult Ocorrencia]          | Descrição Ult Ocorrencia    | wstr(50)     | wstr(50)           |
| 73 | ADO NET Source Output.Columns[Usuário Ocorrencia]                | Usuário Ocorrencia          | wstr(10)     | wstr(10)           |
| 74 | ADO NET Source Output.Columns[UsuarioEmissao]                    | UsuarioEmissao              | wstr(10)     | wstr(10)           |

## Resumo por Tipo de Dado

| Tipo SSIS    | Quantidade de Colunas |
|--------------|-----------------------|
| wstr         | 48                    |
| cy (money)   | 11                    |
| i4 (int)     | 9                     |
| numeric      | 4 (VlFreteOrigem, VlICMSOrigem na origem; mais 11 cy no destino como numeric) |
| dbTimeStamp  | 5                     |
| nText (MAX)  | 1                     |
| **Total**    | **74**                |

## Caminho do Fluxo de Dados

```
ADO NET Source Output
    └── [path: ADO NET Source Output]
        └── ADO NET Destination Input (dbo.fBaseCTE)
```

Path SSIS: `Package\Load fCTE.Paths[ADO NET Source Output]`
