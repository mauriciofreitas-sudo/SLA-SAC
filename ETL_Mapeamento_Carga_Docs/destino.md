# ETL_Mapeamento_Carga — Destino

## Atributos do Servidor de Destino

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |
| Provider | SQLOLEDB.1 |
| ConnectRetryCount | 1 |
| ConnectRetryInterval | 5s |
| ConnectionManager | DBSTAGE |

## Destination Component: Destino OLE DB

| Atributo | Valor |
|---|---|
| Componente | Microsoft.OLEDBDestination |
| Tabela de Destino | [dbo].[tblBasePrincipal] |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | TABLOCK, CHECK_CONSTRAINTS |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadMaxInsertCommitSize | 2.147.483.647 |
| CommandTimeout | 0 (sem timeout) |

## Estrategia de Carga

**DELETE + INSERT (Full Refresh)**

1. `DELETE FROM [dbo].[tblBasePrincipal]` (sem WHERE — deleta tudo).
2. INSERT via FastLoad com TABLOCK.

## Colunas Mapeadas

| # | Coluna Destino | Tipo SSIS | Tamanho | Mapeada de |
|---|---|---|---|---|
| 1 | DsApelidoEmitente | DT_STR | 10 | DsApelidoEmitente |
| 2 | DsEmpDest | DT_STR | 10 | DsEmpDest |
| 3 | nrdoctofiscal | DT_I4 | — | nrdoctofiscal |
| 4 | Previsao de Entrega | DT_STR | 30 | Previsao de Entrega |
| 5 | ValorCTE | DT_CY | — | ValorCTE |
| 6 | Data de Emissao CTE | DT_STR | 30 | Data de Emissao CTE |
| 7 | CdRemetente | DT_STR | 14 | CdRemetente |
| 8 | DsRemetente | DT_STR | 100 | DsRemetente |
| 9 | CdDestinatario | DT_STR | 14 | CdDestinatario |
| 10 | dsDestinatario | DT_STR | 100 | dsDestinatario |
| 11 | CdInscricao | DT_STR | 14 | CdInscricao |
| 12 | dsPagador | DT_STR | 100 | dsPagador |
| 13 | CidadeColeta | DT_STR | 30 | CidadeColeta |
| 14 | DsUFOrigem | DT_STR | 2 | DsUFOrigem |
| 15 | CidadeEntrega | DT_STR | 30 | CidadeEntrega |
| 16 | DsUFDestino | DT_STR | 2 | DsUFDestino |
| 17 | Peso | DT_CY | — | Peso |
| 18 | Valor Mercadoria | DT_CY | — | Valor Mercadoria |
| 19 | Volume | DT_CY | — | Volume |
| 20 | NrNotaFiscal | DT_STR | 255 | NrNotaFiscal |
| 21 | DsItinerario | DT_STR | 7 | DsItinerario |
| 22 | PlacaManifInterior | DT_STR | 8 | PlacaManifInterior |
| 23 | Motorista Manifesto | DT_STR | 1 | Motorista Manifesto |
| 24 | DtSaidaParceiro | DT_STR | 30 | DtSaidaParceiro |
| 25 | DtSaidachegadadestino | DT_STR | 30 | DtSaidachegadadestino |
| 26 | NrRomaneio | DT_STR | 16 | NrRomaneio |
| 27 | Motorista romaneio | DT_STR | 1 | Motorista romaneio |
| 28 | DataRomaneio | DT_STR | 30 | DataRomaneio |
| 29 | DsUltimaOcorrencia | DT_STR | 50 | DsUltimaOcorrencia |
| 30 | DtUltimaOcorrencia | DT_STR | 30 | DtUltimaOcorrencia |

## Configuracoes de Performance

| Configuracao | Valor |
|---|---|
| AccessMode | FastLoad (3) |
| TABLOCK | Ativado |
| CHECK_CONSTRAINTS | Ativado |
| Commit Size | 2.147.483.647 (commit unico) |
| CommandTimeout | 0 (sem limite) |
