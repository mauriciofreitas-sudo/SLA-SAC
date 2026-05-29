# Destino dos Dados — ETL_Auditoria_CTe

## Identificação do Servidor de Destino

| Propriedade | Valor |
|-------------|-------|
| Servidor | 10.100.86.89 |
| Banco de Dados | DBStage |
| Usuario | sqldba |
| Tipo de Conexao | OLEDB — SQLOLEDB.1 |
| Connection Manager | DBSTAGE |

---

## Destino 1: `[dbo].[stg_Auditoria_CTe]` (Destino OLE DB)

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `[dbo].[stg_Auditoria_CTe]` |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | `TABLOCK,CHECK_CONSTRAINTS` |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadMaxInsertCommitSize | 2147483647 (commit unico ao final) |
| CommandTimeout | 0 (infinito) |
| Estrategia | **Full Replace** — DELETE antes do INSERT |

### Colunas Mapeadas — `stg_Auditoria_CTe` (53 colunas)

| # | Coluna | Tipo SSIS | Tamanho |
|---|--------|-----------|---------|
| 1 | CdPercursoComercial | i4 | — |
| 2 | cdempresa | i4 | — |
| 3 | DsApelidoEmitente | str | 10 |
| 4 | CEPColeta | i4 | — |
| 5 | dsusuario | str | 10 |
| 6 | CEPDestino | i4 | — |
| 7 | dsplacaveiculo | str | 8 |
| 8 | NrPlacaReboque1 | str | 8 |
| 9 | DsMotorista | str | 40 |
| 10 | VlFretePeso | numeric | p=14,s=4 |
| 11 | VlFreteValor | numeric | p=14,s=4 |
| 12 | VlPedagio | numeric | p=14,s=4 |
| 13 | VlGRIS | numeric | p=14,s=4 |
| 14 | TotalPrestacao | numeric | p=14,s=4 |
| 15 | nrdoctofiscal | i4 | — |
| 16 | CdTpDoctoFiscal | i4 | — |
| 17 | dsapelido | str | 10 |
| 18 | DtEmissao | dbTimeStamp | — |
| 19 | MesAtual | i4 | — |
| 20 | Natureza | str | 40 |
| 21 | CodNatureza | i4 | — |
| 22 | DsSituacao | str | 9 |
| 23 | DsTpFrete | str | 1 |
| 24 | DsTipoEmissao | str | 16 |
| 25 | ParametroCalculo | str | 9 |
| 26 | DsUFOrigem | str | 2 |
| 27 | DsUFDestino | str | 2 |
| 28 | dtentrega | dbTimeStamp | — |
| 29 | DtEmissaoFatura | dbTimeStamp | — |
| 30 | dtvencimento | dbTimeStamp | — |
| 31 | QtPeso | numeric | p=14,s=4 |
| 32 | QtMetrosCubicos | numeric | p=14,s=4 |
| 33 | VlMercadoria | numeric | p=14,s=4 |
| 34 | VlTotalPrestacao | numeric | p=14,s=4 |
| 35 | VlLiquido | numeric | p=14,s=4 |
| 36 | VlICMS | numeric | p=14,s=4 |
| 37 | QtVolume | numeric | p=14,s=4 |
| 38 | CdRemetente | str | 14 |
| 39 | DsRemetente | str | 100 |
| 40 | CdDestinatario | str | 14 |
| 41 | dsDestinatario | str | 100 |
| 42 | CdConsignatario | str | 14 |
| 43 | dsConsignatario | str | 100 |
| 44 | CdInscricao | str | 14 |
| 45 | dsPagador | str | 100 |
| 46 | CdEmpresaDestino | i4 | — |
| 47 | DsEmpDest | str | 10 |
| 48 | CidadeColeta | str | 30 |
| 49 | CdRegiaoColeta | i4 | — |
| 50 | CidadeEntrega | str | 30 |
| 51 | CdRegiaoEntrega | i4 | — |
| 52 | Vlcad | numeric | p=14,s=4 |
| 53 | VlTRT | numeric | p=14,s=4 |
| 54 | cdfatura | i4 | — |
| 55 | InTDE | str | 3 |
| 56 | DoctoOrigem | i4 | — |
| 57 | NrNotaFiscal | str | 255 |
| 58 | DsSituacaoArq | str | 13 |
| 59 | VlFreteOrigem | numeric | p=14,s=4 |
| 60 | VlICMSOrigem | numeric | p=14,s=4 |
| 61 | dsnatureza | str | 40 |
| 62 | dstransporte | str | 40 |
| 63 | DsUltimaOcorrencia | str | 50 |
| 64 | DtUltimaOcorrencia | dbTimeStamp | — |
| 65 | DataRomaneio | dbTimeStamp | — |
| 66 | NrRomaneio | str | 16 |
| 67 | NrPlacaRomaneio | str | 8 |
| 68 | PlacaManifInterior | str | 8 |
| 69 | DtSaidaParceiro | dbTimeStamp | — |
| 70 | DsItinerario | str | 7 |
| 71 | ArquivoConemb | str | 255 |
| 72 | DtEnvioConemb | dbTimeStamp | — |
| 73 | ArquivoOcoren | str | 255 |
| 74 | DtEnvioOcoren | dbTimeStamp | — |
| 75 | ArquivoDocCob | str | 255 |
| 76 | DtEnvioDocCob | dbTimeStamp | — |
| 77 | Pedido | str | 30 |
| 78 | Conteudo | str | 111 |
| 79 | StatusSubstituido | str | 3 |

---

## Destino 2: `[dbo].[Auditoria_CTe_Cod_Prod]` (Destino OLE DB 1)

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `[dbo].[Auditoria_CTe_Cod_Prod]` |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | `TABLOCK,CHECK_CONSTRAINTS` |
| Estrategia | **Full Replace** — DELETE antes do INSERT |

### Colunas — `Auditoria_CTe_Cod_Prod` (4 colunas mapeadas)

| # | Coluna | Tipo SSIS | Tamanho |
|---|--------|-----------|---------|
| 1 | EmpresaNF | i4 | — |
| 2 | DoctFiscalNF | i4 | — |
| 3 | TpdoctfiscalNF | i4 | — |
| 4 | CdNaturezaNF | i4 | — |

---

## Destino 3: `[dbo].[Auditoria_CTe_Cod_Prod_v2]` (Destino OLE DB 2)

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `[dbo].[Auditoria_CTe_Cod_Prod_v2]` |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | `TABLOCK,CHECK_CONSTRAINTS` |
| Estrategia | **Full Replace** — DELETE antes do INSERT |

### Colunas — `Auditoria_CTe_Cod_Prod_v2` (6 colunas)

| # | Coluna | Tipo SSIS | Tamanho |
|---|--------|-----------|---------|
| 1 | CdProduto | str | 25 |
| 2 | DsNatureza | str | 40 |
| 3 | CdNatureza | i4 | — |
| 4 | EmpresaNF | i4 | — |
| 5 | DoctFiscalNF | i4 | — |
| 6 | TpdoctfiscalNF | i4 | — |

## Configuracoes de Performance (FastLoad)

Todos os 3 destinos OLE DB usam FastLoad com:
- `TABLOCK`: bloqueia a tabela durante o carregamento (melhor throughput)
- `CHECK_CONSTRAINTS`: valida constraints durante o carregamento
- `FastLoadMaxInsertCommitSize = 2147483647`: commit apenas ao final (risco de rollback grande)
