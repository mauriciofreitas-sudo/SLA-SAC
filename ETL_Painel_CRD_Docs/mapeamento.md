# ETL_Painel_CRD — Mapeamento Completo

## Visao Geral

| Aspecto | Valor |
|---|---|
| Servidor Origem | 169.57.181.231 (softran_translute) |
| Servidor Destino | 10.100.86.89 (DBStage) |
| Total de Colunas (Fluxo 1) | 41 |
| Total de Colunas (Fluxo 2) | 21 |
| Total Geral | 62 colunas |
| Fluxos de Dados | 2 |

## Mapeamento Fluxo 1 — VW_BASE_CRD → stg_CRDBase (41 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Grupo |
|---|---|---|---|---|
| 1 | CdEmpresa | DT_I4 | — | Empresa |
| 2 | PontoOperacao | DT_STR | 10 | Empresa |
| 3 | DsGrupoCliente | DT_STR | 100 | Cliente |
| 4 | Destinatario | DT_STR | 100 | Cliente |
| 5 | CidadeDest | DT_STR | 30 | Localizacao |
| 6 | UFDest | DT_STR | 2 | Localizacao |
| 7 | DsRedespacho | DT_STR | 40 | Logistica |
| 8 | CidadeRed | DT_STR | 30 | Localizacao |
| 9 | UFRed | DT_STR | 2 | Localizacao |
| 10 | DtEmissao | DT_DBTIMESTAMP | — | Data |
| 11 | CdTpDoctoFiscal | DT_I4 | — | Fiscal |
| 12 | DsTpDoctoFiscal | DT_STR | 40 | Fiscal |
| 13 | TipoEmissao | DT_STR | 16 | Fiscal |
| 14 | NrDoctoFiscal | DT_I4 | — | Fiscal |
| 15 | VlTotalPrestacao | DT_CY | — | Financeiro |
| 16 | ULTOCO | DT_STR | 50 | Ocorrencia |
| 17 | ULTUSUOCO | DT_STR | 10 | Ocorrencia |
| 18 | ULTDATAOCO | DT_DBTIMESTAMP | — | Ocorrencia |
| 19 | STATUSPROTOCOLO | DT_STR | 12 | Protocolo |
| 20 | BAIXAPROTOCOLO | DT_DBTIMESTAMP | — | Protocolo |
| 21 | USUPREPROTOCOLO | DT_STR | 30 | Protocolo |
| 22 | DATACAIXA | DT_DBTIMESTAMP | — | Caixa |
| 23 | STATUSCAIXA | DT_STR | 11 | Caixa |
| 24 | USUPROTOCOLO | DT_STR | 10 | Protocolo |
| 25 | SitFat | DT_STR | 12 | Faturamento |
| 26 | StatusPagto | DT_STR | 10 | Financeiro |
| 27 | UltRomaneio | DT_I4 | — | Romaneio |
| 28 | DtUltRomaneio | DT_DBTIMESTAMP | — | Romaneio |
| 29 | MotUltRomaneio | DT_STR | 40 | Romaneio |
| 30 | PropUltRomaneio | DT_STR | 40 | Romaneio |
| 31 | UltManifesto | DT_STR | 15 | Manifesto |
| 32 | DtUltManifesto | DT_DBTIMESTAMP | — | Manifesto |
| 33 | MotUltManifesto | DT_STR | 40 | Manifesto |
| 34 | PropUltManifesto | DT_STR | 40 | Manifesto |
| 35 | DsNatureza | DT_STR | 40 | Fiscal |
| 36 | CdFatura | DT_I4 | — | Faturamento |
| 37 | ObsFatura | DT_STR | 500 | Faturamento |
| 38 | NotasFiscais | DT_STR | 255 | Fiscal |
| 39 | DtPrevEntrega | DT_DBTIMESTAMP | — | Data |
| 40 | Empresa Ficha | DT_I4 | — | Ficha |
| 41 | Ficha | DT_I4 | — | Ficha |

## Mapeamento Fluxo 2 — vw_FichaViagens → stg_Base_Ficha_Viagens (21 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Grupo |
|---|---|---|---|---|
| 1 | Empresa | DT_STR | 23 | Empresa |
| 2 | DtEmissao | DT_DBTIMESTAMP | — | Data |
| 3 | CdEmpresa | DT_I4 | — | Empresa |
| 4 | NrFicha | DT_I4 | — | Ficha |
| 5 | CdRotaRMS | DT_I4 | — | Rota |
| 6 | DtFechamento | DT_DBTIMESTAMP | — | Data |
| 7 | NrPlaca | DT_STR | 8 | Veiculo |
| 8 | TipoVeiculo | DT_STR | 40 | Veiculo |
| 9 | NrPlacaReboque1 | DT_STR | 8 | Veiculo |
| 10 | NrPlacaReboque2 | DT_STR | 8 | Veiculo |
| 11 | NrPlacaReboque3 | DT_STR | 10 | Veiculo |
| 12 | Motorista | DT_STR | 40 | Motorista |
| 13 | Proprietario | DT_STR | 60 | Proprietario |
| 14 | CTRB | DT_STR | 21 | Contrato |
| 15 | DtCTRB | DT_DBTIMESTAMP | — | Data |
| 16 | AdtoCTRB | DT_NUMERIC | 38,4 | Financeiro |
| 17 | PedagioCTRB | DT_NUMERIC | 38,4 | Financeiro |
| 18 | NrAcerto | DT_STR | 21 | Acerto |
| 19 | DtAcerto | DT_DBTIMESTAMP | — | Data |
| 20 | DtFechaAcerto | DT_DBTIMESTAMP | — | Data |
| 21 | VlComissao | DT_NUMERIC | 14,4 | Financeiro |

## Totais por Tipo — Fluxo 1

| Tipo SSIS | Quantidade | % |
|---|---|---|
| DT_STR | 26 | 63% |
| DT_I4 | 7 | 17% |
| DT_DBTIMESTAMP | 7 | 17% |
| DT_CY | 1 | 2% |
| **Total** | **41** | **100%** |

## Totais por Tipo — Fluxo 2

| Tipo SSIS | Quantidade | % |
|---|---|---|
| DT_STR | 10 | 48% |
| DT_I4 | 4 | 19% |
| DT_DBTIMESTAMP | 5 | 24% |
| DT_NUMERIC | 3 | 14% |
| **Total** | **21** | **100%** |
