# ETL_Painel_CRD — Transformacoes

## Resumo

Package com **dois fluxos independentes**, ambos passthrough. Nao ha transformacoes intermediarias. Toda a logica SQL esta encapsulada nas views `VW_BASE_CRD` e `vw_FichaViagens` no Softran.

## Fluxo 1: DATA LAKE - BASE - CRD

### Diagrama ASCII

```
[Origem OLE DB]
  SELECT * FROM [dbo].[VW_BASE_CRD] WITH (NOLOCK)
  WHERE DtEmissao >= GETDATE()-350 AND DtEmissao <= GETDATE()
  Timeout=300s
  (169.57.181.231 / softran_translute)
         |
         | 41 colunas passthrough
         |
[Destino OLE DB]
  INSERT → [dbo].[stg_CRDBase]
  (10.100.86.89 / DBStage)
  FastLoad TABLOCK
```

### Pre-Processamento

```sql
DELETE FROM [dbo].[stg_CRDBase]
```

(ThreadHint=1 — executado em thread paralela com o container de fichas)

## Fluxo 2: DATA LAKE - CRD - Fichas viagens

### Diagrama ASCII

```
[Origem CRD]
  SELECT * FROM [dbo].[vw_FichaViagens]with (nolock)
  [nota: typo — falta espaco antes de "with"]
  Timeout=0 (infinito)
  (169.57.181.231 / softran_translute)
         |
         | 21 colunas passthrough
         |
[DEST CRD]
  INSERT → [dbo].[stg_Base_Ficha_Viagens]
  (10.100.86.89 / DBStage)
  FastLoad TABLOCK
```

## Mapeamento de Tipos — Fluxo 1 (stg_CRDBase)

| Coluna | Tipo SSIS | Codepage | Observacao |
|---|---|---|---|
| CdEmpresa | DT_I4 | — | Chave da empresa |
| PontoOperacao | DT_STR | 1252 | varchar |
| DsGrupoCliente | DT_STR | 1252 | varchar(100) |
| Destinatario | DT_STR | 1252 | varchar(100) |
| CidadeDest | DT_STR | 1252 | varchar(30) |
| UFDest | DT_STR | 1252 | varchar(2) |
| DsRedespacho | DT_STR | 1252 | varchar(40) |
| CidadeRed | DT_STR | 1252 | varchar(30) |
| UFRed | DT_STR | 1252 | varchar(2) |
| DtEmissao | DT_DBTIMESTAMP | — | datetime — OK |
| CdTpDoctoFiscal | DT_I4 | — | |
| DsTpDoctoFiscal | DT_STR | 1252 | varchar(40) |
| TipoEmissao | DT_STR | 1252 | varchar(16) |
| NrDoctoFiscal | DT_I4 | — | |
| VlTotalPrestacao | DT_CY | — | money |
| ULTOCO | DT_STR | 1252 | varchar(50) |
| ULTUSUOCO | DT_STR | 1252 | varchar(10) |
| ULTDATAOCO | DT_DBTIMESTAMP | — | datetime — OK |
| STATUSPROTOCOLO | DT_STR | 1252 | varchar(12) |
| BAIXAPROTOCOLO | DT_DBTIMESTAMP | — | datetime |
| USUPREPROTOCOLO | DT_STR | 1252 | varchar(30) |
| DATACAIXA | DT_DBTIMESTAMP | — | datetime |
| STATUSCAIXA | DT_STR | 1252 | varchar(11) |
| USUPROTOCOLO | DT_STR | 1252 | varchar(10) |
| SitFat | DT_STR | 1252 | varchar(12) |
| StatusPagto | DT_STR | 1252 | varchar(10) |
| UltRomaneio | DT_I4 | — | |
| DtUltRomaneio | DT_DBTIMESTAMP | — | datetime |
| MotUltRomaneio | DT_STR | 1252 | varchar(40) |
| PropUltRomaneio | DT_STR | 1252 | varchar(40) |
| UltManifesto | DT_STR | 1252 | varchar(15) |
| DtUltManifesto | DT_DBTIMESTAMP | — | datetime |
| MotUltManifesto | DT_STR | 1252 | varchar(40) |
| PropUltManifesto | DT_STR | 1252 | varchar(40) |
| DsNatureza | DT_STR | 1252 | varchar(40) |
| CdFatura | DT_I4 | — | |
| ObsFatura | DT_STR | 1252 | varchar(500) — campo grande |
| NotasFiscais | DT_STR | 1252 | varchar(255) |
| DtPrevEntrega | DT_DBTIMESTAMP | — | datetime — OK |
| Empresa Ficha | DT_I4 | — | Nome com espaco |
| Ficha | DT_I4 | — | |

## Mapeamento de Tipos — Fluxo 2 (stg_Base_Ficha_Viagens)

| Coluna | Tipo SSIS | Tamanho/Precisao |
|---|---|---|
| Empresa | DT_STR | 23 |
| DtEmissao | DT_DBTIMESTAMP | — |
| CdEmpresa | DT_I4 | — |
| NrFicha | DT_I4 | — |
| CdRotaRMS | DT_I4 | — |
| DtFechamento | DT_DBTIMESTAMP | — |
| NrPlaca | DT_STR | 8 |
| TipoVeiculo | DT_STR | 40 |
| NrPlacaReboque1 | DT_STR | 8 |
| NrPlacaReboque2 | DT_STR | 8 |
| NrPlacaReboque3 | DT_STR | 10 |
| Motorista | DT_STR | 40 |
| Proprietario | DT_STR | 60 |
| CTRB | DT_STR | 21 |
| DtCTRB | DT_DBTIMESTAMP | — |
| AdtoCTRB | DT_NUMERIC | 38,4 |
| PedagioCTRB | DT_NUMERIC | 38,4 |
| NrAcerto | DT_STR | 21 |
| DtAcerto | DT_DBTIMESTAMP | — |
| DtFechaAcerto | DT_DBTIMESTAMP | — |
| VlComissao | DT_NUMERIC | 14,4 |
