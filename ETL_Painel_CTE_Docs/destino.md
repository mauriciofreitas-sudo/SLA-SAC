# ETL_Painel_CTE — Destino dos Dados

## Servidor de Destino

| Item              | Valor                                          |
|-------------------|------------------------------------------------|
| Servidor          | 10.100.86.89                                   |
| Banco             | DWGrupolc                                      |
| Usuário           | sqldba                                         |
| Connection Name   | 10.100.86.89.DWGrupolc.sqldba (ADO.NET)        |
| Connection OLEDB  | 10.100.86.89.DWGrupolc.sqldba1 (OLEDB/MSOLEDBSQL) |

## Estratégia de Carga

| Item                    | Detalhe                                              |
|-------------------------|------------------------------------------------------|
| Estratégia              | Full Reload (TRUNCATE + INSERT)                      |
| Pré-processamento       | TRUNCATE TABLE "fBaseCTE" (task "Truncate fCTE")     |
| Método de insert        | ADO.NET com SqlBulkCopy habilitado                   |
| BatchSize               | 0 (usa buffer interno do SSIS)                       |
| CommandTimeout destino  | 30 segundos                                          |
| validateExternalMetadata| False (sem validação de metadados externos)          |

## Tabela de Destino: `dbo.fBaseCTE`

| # | Nome da Coluna              | Tipo Destino (External) | Precisão/Escala/Tamanho         |
|---|-----------------------------|-------------------------|---------------------------------|
| 1 | cdempresa                   | wstr                    | 50                              |
| 2 | DsApelidoEmitente           | wstr                    | 3                               |
| 3 | CEPColeta                   | i4                      | —                               |
| 4 | CEPDestino                  | i4                      | —                               |
| 5 | dsplacaveiculo              | wstr                    | 8                               |
| 6 | NrPlacaReboque1             | wstr                    | 8                               |
| 7 | DsMotorista                 | wstr                    | 40                              |
| 8 | VlFretePeso                 | numeric                 | p=19, s=255                     |
| 9 | VlFreteValor                | numeric                 | p=19, s=255                     |
| 10 | VlPedagio                  | numeric                 | p=19, s=255                     |
| 11 | VlGRIS                     | numeric                 | p=19, s=255                     |
| 12 | VlTotalPrestacao           | numeric                 | p=19, s=255                     |
| 13 | nrdoctofiscal              | i4                      | —                               |
| 14 | CdTpDoctoFiscal            | i4                      | —                               |
| 15 | dsapelido                  | wstr                    | 10                              |
| 16 | DtEmissao                  | wstr                    | 30                              |
| 17 | DsSituacao                 | wstr                    | 9                               |
| 18 | DsTpFrete                  | wstr                    | 1                               |
| 19 | DsTipoEmissao              | wstr                    | 16                              |
| 20 | DsUFOrigem                 | wstr                    | 2                               |
| 21 | DsUFDestino                | wstr                    | 2                               |
| 22 | dtentrega                  | wstr                    | 30                              |
| 23 | DtEmissaoFatura            | wstr                    | 30                              |
| 24 | dtvencimento               | wstr                    | 30                              |
| 25 | QtPeso                     | numeric                 | p=19, s=255                     |
| 26 | QtMetrosCubicos            | numeric                 | p=19, s=255                     |
| 27 | VlMercadoria               | numeric                 | p=19, s=255                     |
| 28 | VlLiquido                  | numeric                 | p=19, s=255                     |
| 29 | VlICMS                     | numeric                 | p=19, s=255                     |
| 30 | QtVolume                   | numeric                 | p=19, s=255                     |
| 31 | CdRemetente                | wstr                    | 14                              |
| 32 | DsRemetente                | wstr                    | 100                             |
| 33 | CdDestinatario             | wstr                    | 14                              |
| 34 | dsDestinatario             | wstr                    | 100                             |
| 35 | CdConsignatario            | wstr                    | 14                              |
| 36 | dsConsignatario            | wstr                    | 100                             |
| 37 | CdInscricao                | wstr                    | 14                              |
| 38 | dsPagador                  | wstr                    | 100                             |
| 39 | CdEmpresaDestino           | i4                      | —                               |
| 40 | DsEmpDest                  | wstr                    | 10                              |
| 41 | CidadeColeta               | wstr                    | 30                              |
| 42 | CidadeEntrega              | wstr                    | 30                              |
| 43 | cdfatura                   | i4                      | —                               |
| 44 | InTDE                      | wstr                    | 3                               |
| 45 | DoctoOrigem                | i4                      | —                               |
| 46 | NrNotaFiscal               | wstr                    | 2147483647 (MAX / nText)        |
| 47 | DsSituacaoArq              | wstr                    | 13                              |
| 48 | VlFreteOrigem              | numeric                 | p=14, s=4                       |
| 49 | VlICMSOrigem               | numeric                 | p=14, s=4                       |
| 50 | dsnatureza                 | wstr                    | 40                              |
| 51 | dstransporte               | wstr                    | 40                              |
| 52 | DsUltimaOcorrencia         | wstr                    | 50                              |
| 53 | DtUltimaOcorrencia         | wstr                    | 30                              |
| 54 | DsUsuario                  | wstr                    | 10                              |
| 55 | NomeMotorista              | wstr                    | 40                              |
| 56 | NrRomaneio                 | wstr                    | 16                              |
| 57 | NrPlacaRomaneio            | wstr                    | 8                               |
| 58 | PlacaManifInterior         | wstr                    | 8                               |
| 59 | DtSaidaParceiro            | dbTimeStamp             | —                               |
| 60 | DsItinerario               | wstr                    | 7                               |
| 61 | ArquivoConemb              | wstr                    | 255                             |
| 62 | DtEnvioConemb              | dbTimeStamp             | —                               |
| 63 | ArquivoOcoren              | wstr                    | 255                             |
| 64 | DtEnvioOcoren              | dbTimeStamp             | —                               |
| 65 | ArquivoDocCob              | wstr                    | 255                             |
| 66 | DtEnvioDocCob              | dbTimeStamp             | —                               |
| 67 | Pedido                     | wstr                    | 30                              |
| 68 | Conteudo                   | wstr                    | 111                             |
| 69 | Previsão de Entrega        | wstr                    | 30                              |
| 70 | Dt Inclusao Ocorrência     | wstr                    | 61                              |
| 71 | Dt Ocorrencia              | wstr                    | 61                              |
| 72 | Descrição Ult Ocorrencia   | wstr                    | 50                              |
| 73 | Usuário Ocorrencia         | wstr                    | 10                              |
| 74 | UsuarioEmissao             | wstr                    | 10                              |

**Total: 74 colunas**

## Pré-processamento SQL (Task "Truncate fCTE")

```sql
TRUNCATE TABLE "fBaseCTE"
```

- Conexão usada: 10.100.86.89.DWGrupolc.sqldba1 (OLEDB/MSOLEDBSQL)
- ThreadHint: 0 (execução serial)
- Executado ANTES do Data Flow
