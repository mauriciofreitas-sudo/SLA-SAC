# ETL_Painel_CRD — Destino

## Atributos do Servidor de Destino

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |
| Provider | SQLOLEDB.1 |
| ConnectionManager | DBSTAGE |

## Fluxo 1 — Destination: Destino OLE DB (stg_CRDBase)

| Atributo | Valor |
|---|---|
| Componente | Microsoft.OLEDBDestination |
| Tabela de Destino | [dbo].[stg_CRDBase] |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | TABLOCK, CHECK_CONSTRAINTS |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadMaxInsertCommitSize | 2.147.483.647 |
| CommandTimeout | 0 (sem timeout) |

### Estrategia: DELETE + INSERT

Pre-carga: `DELETE FROM [dbo].[stg_CRDBase]` (ThreadHint=1, sem WHERE).

### Colunas Mapeadas — stg_CRDBase (41 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Observacao |
|---|---|---|---|---|
| 1 | CdEmpresa | DT_I4 | — | |
| 2 | PontoOperacao | DT_STR | 10 | |
| 3 | DsGrupoCliente | DT_STR | 100 | |
| 4 | Destinatario | DT_STR | 100 | |
| 5 | CidadeDest | DT_STR | 30 | |
| 6 | UFDest | DT_STR | 2 | |
| 7 | DsRedespacho | DT_STR | 40 | |
| 8 | CidadeRed | DT_STR | 30 | |
| 9 | UFRed | DT_STR | 2 | |
| 10 | DtEmissao | DT_DBTIMESTAMP | — | |
| 11 | CdTpDoctoFiscal | DT_I4 | — | |
| 12 | DsTpDoctoFiscal | DT_STR | 40 | |
| 13 | TipoEmissao | DT_STR | 16 | |
| 14 | NrDoctoFiscal | DT_I4 | — | |
| 15 | VlTotalPrestacao | DT_CY | — | |
| 16 | ULTOCO | DT_STR | 50 | |
| 17 | ULTUSUOCO | DT_STR | 10 | |
| 18 | ULTDATAOCO | DT_DBTIMESTAMP | — | |
| 19 | STATUSPROTOCOLO | DT_STR | 12 | |
| 20 | BAIXAPROTOCOLO | DT_DBTIMESTAMP | — | |
| 21 | USUPREPROTOCOLO | DT_STR | 30 | |
| 22 | DATACAIXA | DT_DBTIMESTAMP | — | |
| 23 | STATUSCAIXA | DT_STR | 11 | |
| 24 | USUPROTOCOLO | DT_STR | 10 | |
| 25 | SitFat | DT_STR | 12 | |
| 26 | StatusPagto | DT_STR | 10 | |
| 27 | UltRomaneio | DT_I4 | — | |
| 28 | DtUltRomaneio | DT_DBTIMESTAMP | — | |
| 29 | MotUltRomaneio | DT_STR | 40 | |
| 30 | PropUltRomaneio | DT_STR | 40 | |
| 31 | UltManifesto | DT_STR | 15 | |
| 32 | DtUltManifesto | DT_DBTIMESTAMP | — | |
| 33 | MotUltManifesto | DT_STR | 40 | |
| 34 | PropUltManifesto | DT_STR | 40 | |
| 35 | DsNatureza | DT_STR | 40 | |
| 36 | CdFatura | DT_I4 | — | |
| 37 | ObsFatura | DT_STR | 500 | Campo longo |
| 38 | NotasFiscais | DT_STR | 255 | |
| 39 | DtPrevEntrega | DT_DBTIMESTAMP | — | |
| 40 | Empresa Ficha | DT_I4 | — | Nome com espaco |
| 41 | Ficha | DT_I4 | — | |

## Fluxo 2 — Destination: DEST CRD (stg_Base_Ficha_Viagens)

| Atributo | Valor |
|---|---|
| Componente | Microsoft.OLEDBDestination |
| Tabela de Destino | [dbo].[stg_Base_Ficha_Viagens] |
| AccessMode | 3 (FastLoad) |
| FastLoadOptions | TABLOCK, CHECK_CONSTRAINTS |
| FastLoadMaxInsertCommitSize | 2.147.483.647 |
| CommandTimeout | 0 |

### Estrategia: DELETE nao identificado (sem task SQL previa no Sequence Container)

Nao ha task de DELETE antes do fluxo de fichas — possivel APPEND ou a tabela eh truncada externamente.

### Colunas Mapeadas — stg_Base_Ficha_Viagens (21 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao |
|---|---|---|---|
| 1 | Empresa | DT_STR | 23 |
| 2 | DtEmissao | DT_DBTIMESTAMP | — |
| 3 | CdEmpresa | DT_I4 | — |
| 4 | NrFicha | DT_I4 | — |
| 5 | CdRotaRMS | DT_I4 | — |
| 6 | DtFechamento | DT_DBTIMESTAMP | — |
| 7 | NrPlaca | DT_STR | 8 |
| 8 | TipoVeiculo | DT_STR | 40 |
| 9 | NrPlacaReboque1 | DT_STR | 8 |
| 10 | NrPlacaReboque2 | DT_STR | 8 |
| 11 | NrPlacaReboque3 | DT_STR | 10 |
| 12 | Motorista | DT_STR | 40 |
| 13 | Proprietario | DT_STR | 60 |
| 14 | CTRB | DT_STR | 21 |
| 15 | DtCTRB | DT_DBTIMESTAMP | — |
| 16 | AdtoCTRB | DT_NUMERIC | 38,4 |
| 17 | PedagioCTRB | DT_NUMERIC | 38,4 |
| 18 | NrAcerto | DT_STR | 21 |
| 19 | DtAcerto | DT_DBTIMESTAMP | — |
| 20 | DtFechaAcerto | DT_DBTIMESTAMP | — |
| 21 | VlComissao | DT_NUMERIC | 14,4 |
