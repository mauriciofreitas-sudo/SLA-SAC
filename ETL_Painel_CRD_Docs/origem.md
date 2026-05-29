# ETL_Painel_CRD — Origem

## Atributos dos Servidores de Origem

| Atributo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | softran_translute |
| Usuario | softran |
| Provider | SQLOLEDB.1 |
| ConnectionManager | SOFTRAN - TRANSLUTE |

## Fluxo 1: Origem OLE DB (DATA LAKE - BASE - CRD)

| Atributo | Valor |
|---|---|
| ComponentClassID | Microsoft.OLEDBSource |
| AccessMode | 2 (SQL Command) |
| CommandTimeout | 300 segundos |
| DefaultCodePage | 1252 |

### Query SQL

```sql
select * from [dbo].[VW_BASE_CRD] with (nolock)
where DtEmissao >= getdate() -350 and DtEmissao <= GETDATE()
```

### Colunas de Saida — stg_CRDBase (35 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Descricao Semantica |
|---|---|---|---|---|
| 1 | CdEmpresa | DT_I4 | — | Codigo da empresa emissora |
| 2 | PontoOperacao | DT_STR | 10 | Ponto de operacao/filial |
| 3 | DsGrupoCliente | DT_STR | 100 | Descricao do grupo de cliente |
| 4 | Destinatario | DT_STR | 100 | Nome do destinatario |
| 5 | CidadeDest | DT_STR | 30 | Cidade de destino |
| 6 | UFDest | DT_STR | 2 | UF de destino |
| 7 | DsRedespacho | DT_STR | 40 | Nome da empresa de redespacho |
| 8 | CidadeRed | DT_STR | 30 | Cidade do redespacho |
| 9 | UFRed | DT_STR | 2 | UF do redespacho |
| 10 | DtEmissao | DT_DBTIMESTAMP | — | Data de emissao do conhecimento |
| 11 | CdTpDoctoFiscal | DT_I4 | — | Codigo do tipo de documento fiscal |
| 12 | DsTpDoctoFiscal | DT_STR | 40 | Descricao do tipo de documento fiscal |
| 13 | TipoEmissao | DT_STR | 16 | Tipo de emissao (Normal, Cancelado, etc.) |
| 14 | NrDoctoFiscal | DT_I4 | — | Numero do documento fiscal |
| 15 | VlTotalPrestacao | DT_CY | — | Valor total da prestacao de servico |
| 16 | ULTOCO | DT_STR | 50 | Ultima ocorrencia de entrega |
| 17 | ULTUSUOCO | DT_STR | 10 | Usuario da ultima ocorrencia |
| 18 | ULTDATAOCO | DT_DBTIMESTAMP | — | Data da ultima ocorrencia |
| 19 | STATUSPROTOCOLO | DT_STR | 12 | Status do protocolo de entrega |
| 20 | BAIXAPROTOCOLO | DT_DBTIMESTAMP | — | Data de baixa do protocolo |
| 21 | USUPREPROTOCOLO | DT_STR | 30 | Usuario pre-protocolo |
| 22 | DATACAIXA | DT_DBTIMESTAMP | — | Data de registro em caixa |
| 23 | STATUSCAIXA | DT_STR | 11 | Status do caixa |
| 24 | USUPROTOCOLO | DT_STR | 10 | Usuario do protocolo |
| 25 | SitFat | DT_STR | 12 | Situacao do faturamento |
| 26 | StatusPagto | DT_STR | 10 | Status do pagamento |
| 27 | UltRomaneio | DT_I4 | — | Numero do ultimo romaneio |
| 28 | DtUltRomaneio | DT_DBTIMESTAMP | — | Data do ultimo romaneio |
| 29 | MotUltRomaneio | DT_STR | 40 | Motorista do ultimo romaneio |
| 30 | PropUltRomaneio | DT_STR | 40 | Proprietario do ultimo romaneio |
| 31 | UltManifesto | DT_STR | 15 | Numero do ultimo manifesto |
| 32 | DtUltManifesto | DT_DBTIMESTAMP | — | Data do ultimo manifesto |
| 33 | MotUltManifesto | DT_STR | 40 | Motorista do ultimo manifesto |
| 34 | PropUltManifesto | DT_STR | 40 | Proprietario do ultimo manifesto |
| 35 | DsNatureza | DT_STR | 40 | Natureza da carga |
| 36 | CdFatura | DT_I4 | — | Codigo da fatura |
| 37 | ObsFatura | DT_STR | 500 | Observacoes da fatura |
| 38 | NotasFiscais | DT_STR | 255 | Notas fiscais concatenadas |
| 39 | DtPrevEntrega | DT_DBTIMESTAMP | — | Data prevista de entrega |
| 40 | Empresa Ficha | DT_I4 | — | Empresa da ficha de viagem |
| 41 | Ficha | DT_I4 | — | Numero da ficha de viagem |

**Total Fluxo 1: 41 colunas**

## Fluxo 2: Origem CRD (DATA LAKE - CRD - Fichas viagens)

| Atributo | Valor |
|---|---|
| ComponentClassID | Microsoft.OLEDBSource |
| AccessMode | 2 (SQL Command) |
| CommandTimeout | 0 (sem timeout) |
| DefaultCodePage | 1252 |

### Query SQL

```sql
select * from [dbo].[vw_FichaViagens]with (nolock)
```

> Nota: falta espaco antes de `with` — typo na query.

### Colunas de Saida — stg_Base_Ficha_Viagens (21 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Descricao Semantica |
|---|---|---|---|---|
| 1 | Empresa | DT_STR | 23 | Descricao da empresa |
| 2 | DtEmissao | DT_DBTIMESTAMP | — | Data de emissao da ficha |
| 3 | CdEmpresa | DT_I4 | — | Codigo da empresa |
| 4 | NrFicha | DT_I4 | — | Numero da ficha de viagem |
| 5 | CdRotaRMS | DT_I4 | — | Codigo da rota no sistema RMS |
| 6 | DtFechamento | DT_DBTIMESTAMP | — | Data de fechamento da ficha |
| 7 | NrPlaca | DT_STR | 8 | Placa do veiculo |
| 8 | TipoVeiculo | DT_STR | 40 | Tipo/categoria do veiculo |
| 9 | NrPlacaReboque1 | DT_STR | 8 | Placa do reboque 1 |
| 10 | NrPlacaReboque2 | DT_STR | 8 | Placa do reboque 2 |
| 11 | NrPlacaReboque3 | DT_STR | 10 | Placa do reboque 3 |
| 12 | Motorista | DT_STR | 40 | Nome do motorista |
| 13 | Proprietario | DT_STR | 60 | Nome do proprietario do veiculo |
| 14 | CTRB | DT_STR | 21 | Numero do CTRB (contrato de transporte) |
| 15 | DtCTRB | DT_DBTIMESTAMP | — | Data do CTRB |
| 16 | AdtoCTRB | DT_NUMERIC | 38,4 | Adiantamento do CTRB |
| 17 | PedagioCTRB | DT_NUMERIC | 38,4 | Pedagio do CTRB |
| 18 | NrAcerto | DT_STR | 21 | Numero do acerto financeiro |
| 19 | DtAcerto | DT_DBTIMESTAMP | — | Data do acerto |
| 20 | DtFechaAcerto | DT_DBTIMESTAMP | — | Data de fechamento do acerto |
| 21 | VlComissao | DT_NUMERIC | 14,4 | Valor da comissao do motorista |

**Total Fluxo 2: 21 colunas**
