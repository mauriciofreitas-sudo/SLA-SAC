# ETL_Emissao_CTRB — Destino de Dados

## Servidor de Destino

| Campo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |

---

## Data Flow 1: Tarefa Fluxo de Dados (DESABILITADO — Sequence Container)

### Componente Destination

| Campo | Valor |
|---|---|
| Nome | OLE DB Destination |
| Tipo | OLE DB Destination |
| Tabela de Destino | `[dbo].[f_base_emissao_ctrb]` |
| Connection Manager | DBSTAGE (OLEDB SQLOLEDB.1) |
| AccessMode | 0 (OpenRowset — nome direto da tabela) |
| CommandTimeout | 0 (infinito) |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadOptions | TABLOCK,CHECK_CONSTRAINTS |
| FastLoadMaxInsertCommitSize | 2147483647 |
| DefaultCodePage | 1252 |
| errorOrTruncationOperation | Insert |
| errorRowDisposition | RedirectRow (redireciona linhas com erro) |

### Pre-processamento (Task DESABILITADA)

```sql
-- Delete Table (DISABLED):
delete FROM [dbo].[stg_Base_Emissão_CTRB]
```

### Schema de Destino — Tabela f_base_emissao_ctrb (55 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao |
|---|---|---|---|
| 1 | DsEmpresa | wstr | 10 |
| 2 | NrFicha | wstr | 15 |
| 3 | QrKmRodado | numeric | p14,s4 |
| 4 | DtInclusao | wstr | 30 |
| 5 | DtEmissao | dbTimeStamp | — |
| 6 | DtSaida | wstr | 30 |
| 7 | NrPlaca | wstr | 8 |
| 8 | NrPlacaReboque1 | wstr | 8 |
| 9 | NrPlacaReboque2 | wstr | 8 |
| 10 | NrPlacaReboque3 | wstr | 8 |
| 11 | DsMotorista | wstr | 40 |
| 12 | DsProprietario | wstr | 60 |
| 13 | DsTipoFornecedor | wstr | 2 |
| 14 | DsLacre | i4 | — |
| 15 | DsFilialDestManifesto | wstr | 10 |
| 16 | DsCidadeOrigem | wstr | 33 |
| 17 | DsDestino | wstr | 33 |
| 18 | DsRota | wstr | 255 |
| 19 | CdRota | i4 | — |
| 20 | CDTARIFA | i4 | — |
| 21 | TARIFA | wstr | 255 |
| 22 | DsCliente | wstr | 100 |
| 23 | DsMaiorCliente | wstr | 100 |
| 24 | DsTipoTransporte | wstr | 40 |
| 25 | DsTipoTranspPred | wstr | 40 |
| 26 | DsObservacaoCTRB | wstr | 1000 |
| 27 | DsEmissorCTRB | wstr | 10 |
| 28 | VlMercadoria | numeric | p14,s4 |
| 29 | NrCTRB | i4 | — |
| 30 | ValorTotalCTRB | numeric | p38,s4 |
| 31 | VlFreteCTRB | numeric | p38,s4 |
| 32 | VlAdiantamento | numeric | p38,s4 |
| 33 | VlPedagioCTRB | numeric | p38,s4 |
| 34 | SaldoCTRB | numeric | p38,s4 |
| 35 | TipoVinculo | wstr | 8 |
| 36 | NrDoctoFiscal | wstr | 25 |
| 37 | NrRomaneio | wstr | 25 |
| 38 | QtPeso | numeric | p14,s4 |
| 39 | VlLiquido | numeric | p14,s4 |
| 40 | PesoTotalFicha | numeric | p38,s4 |
| 41 | VlCustoTransferencia | numeric | p38,s6 |
| 42 | VlCustoEntrega | numeric | p38,s4 |
| 43 | DsTipoVeiculo | wstr | 8 |
| 44 | DtCTRB | wstr | 30 |
| 45 | Qtde | i4 | — |
| 46 | QTVISITA | i4 | — |
| 47 | VlComissaoFV | numeric | p14,s4 |
| 48 | VlAcrescimosFV | numeric | p38,s13 |
| 49 | VlDescontosFV | numeric | p38,s13 |
| 50 | VlIRRF | numeric | p38,s4 |
| 51 | VlSESTSENAT | numeric | p38,s4 |
| 52 | VlINSS | numeric | p38,s4 |
| 53 | AcrescimosCTRB | numeric | p38,s4 |
| 54 | DescontosCTRB | numeric | p38,s4 |
| 55 | ContratoRepom | wstr | 30 |

**Total: 55 colunas**

## Estrategia de Carga

**Modo: DELETE + Reload (mas DESABILITADO)**

A task `Delete Table` executa `delete FROM [dbo].[stg_Base_Emissão_CTRB]` antes do Data Flow, mas ambas as tasks estao desabilitadas. O fluxo efetivo atual depende do Data Flow raiz (fora do container desabilitado).
