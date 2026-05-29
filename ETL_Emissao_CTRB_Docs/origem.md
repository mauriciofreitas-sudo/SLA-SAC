# ETL_Emissao_CTRB — Origem de Dados

## Servidor de Origem

| Campo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | softran_translute (SOFTRAN_TRANSLUTE) |
| Usuario | softran |
| Connection Manager (ativo) | SOFTRAN - TRANSLUTE (OLEDB SQLOLEDB.1) |
| CommandTimeout da Origem | 300 segundos |

---

## Data Flow 1: Tarefa Fluxo de Dados (DESABILITADO — dentro do Sequence Container)

### Componente Source

| Campo | Valor |
|---|---|
| Nome | Origem OLE DB |
| Tipo | OLE DB Source |
| Objeto/View | `[dbo].[vw_f_Base_Emissão_CTRB]` (OpenRowset) |
| SQL Efetivo | `Select * from [dbo].[vw_Base_Emissão_CTRB] with (nolock)` |
| AccessMode | 0 (SqlCommand) |
| CommandTimeout | 300 segundos |
| DefaultCodePage | 1252 |
| Connection | SOFTRAN - TRANSLUTE |

### Colunas de Saida — Origem OLE DB (Tarefa Desabilitada)

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Codepage |
|---|---|---|---|---|
| 1 | DsEmpresa | str | 10 | 1252 |
| 2 | NrFicha | str | 15 | 1252 |
| 3 | QrKmRodado | numeric | p14,s4 | — |
| 4 | DtInclusao | str | 30 | 1252 |
| 5 | DtEmissao | dbTimeStamp | — | — |
| 6 | DtSaida | str | 30 | 1252 |
| 7 | NrPlaca | str | 8 | 1252 |
| 8 | NrPlacaReboque1 | str | 8 | 1252 |
| 9 | NrPlacaReboque2 | str | 8 | 1252 |
| 10 | NrPlacaReboque3 | str | 8 | 1252 |
| 11 | DsMotorista | str | 40 | 1252 |
| 12 | DsProprietario | str | 60 | 1252 |
| 13 | DsTipoFornecedor | str | 2 | 1252 |
| 14 | DsLacre | i4 | — | — |
| 15 | DsFilialDestManifesto | str | 10 | 1252 |
| 16 | DsCidadeOrigem | str | 33 | 1252 |
| 17 | DsDestino | str | 33 | 1252 |
| 18 | DsRota | str | 255 | 1252 |
| 19 | CdRota | i4 | — | — |
| 20 | CDTARIFA | i4 | — | — |
| 21 | TARIFA | str | 255 | 1252 |
| 22 | DsCliente | str | 100 | 1252 |
| 23 | DsMaiorCliente | str | 100 | 1252 |
| 24 | DsTipoTransporte | str | 40 | 1252 |
| 25 | DsTipoTranspPred | str | 40 | 1252 |
| 26 | DsObservacaoCTRB | str | 1000 | 1252 |
| 27 | DsEmissorCTRB | str | 10 | 1252 |
| 28 | VlMercadoria | numeric | p14,s4 | — |
| 29 | NrCTRB | i4 | — | — |
| 30 | ValorTotalCTRB | numeric | p38,s4 | — |
| 31 | VlFreteCTRB | numeric | p38,s13 | — |
| 32 | VlAdiantamento | numeric | p38,s13 | — |
| 33 | VlPedagioCTRB | numeric | p38,s13 | — |
| 34 | SaldoCTRB | numeric | p38,s4 | — |
| 35 | TipoVinculo | str | 8 | 1252 |
| 36 | NrDoctoFiscal | str | 25 | 1252 |
| 37 | NrRomaneio | str | 25 | 1252 |
| 38 | QtPeso | numeric | p14,s4 | — |
| 39 | VlLiquido | numeric | p14,s4 | — |
| 40 | PesoTotalFicha | numeric | p38,s4 | — |
| 41 | VlCustoTransferencia | numeric | p38,s13 | — |
| 42 | VlCustoEntrega | numeric | p38,s13 | — |
| 43 | DsTipoVeiculo | str | 8 | 1252 |
| 44 | DtCTRB | str | 30 | 1252 |
| 45 | Qtde | i4 | — | — |
| 46 | QTVISITA | i4 | — | — |
| 47 | VlComissaoFV | numeric | p14,s4 | — |
| 48 | VlAcrescimosFV | numeric | p38,s13 | — |
| 49 | VlDescontosFV | numeric | p38,s13 | — |
| 50 | VlIRRF | numeric | p38,s4 | — |
| 51 | VlSESTSENAT | numeric | p38,s4 | — |
| 52 | VlINSS | numeric | p38,s4 | — |
| 53 | AcrescimosCTRB | numeric | p38,s4 | — |
| 54 | DescontosCTRB | numeric | p38,s4 | — |
| 55 | ContratoRepom | str | 30 | 1252 |

**Total de colunas (Data Flow desabilitado): 55**

---

## Nota sobre Data Flow Ativo

O Data Flow principal (ativo no package raiz) usa a mesma view `vw_Base_Emissão_CTRB` via connection `SOFTRAN - TRANSLUTE` (OLEDB). O esquema de colunas e identico ao listado acima.
