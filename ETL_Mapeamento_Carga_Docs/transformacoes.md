# ETL_Mapeamento_Carga — Transformacoes

## Resumo

Este package e um **passthrough puro**: nenhuma transformacao intermediaria. Os dados da view/tabela `MapeamentodeCargas` fluem diretamente para `tblBasePrincipal`. Porem ha campos problematicos onde datas sao transportadas como string (DT_STR de 30 chars).

## Diagrama ASCII do Data Flow

```
[Origem OLE DB]
  SELECT * FROM [dbo].[MapeamentodeCargas] WITH (NOLOCK)
  (169.57.181.231 / SOFTRAN_TRANSLUTE)
         |
         | 30 colunas passthrough (sem transformacao)
         |
[Destino OLE DB]
  INSERT → [dbo].[tblBasePrincipal]
  (10.100.86.89 / DBStage)
```

## Pre-Processamento SQL

```sql
DELETE FROM [dbo].[tblBasePrincipal]
```

Sem WHERE — carga total.

## Componentes de Transformacao

**Nenhum.** Nao ha Derived Column, Data Conversion, Lookup, etc.

## Mapeamento de Tipos

| # | Coluna | Tipo Softran (inferido) | Tipo SSIS | Tipo Destino | Problema |
|---|---|---|---|---|---|
| 1 | DsApelidoEmitente | varchar(10) | DT_STR(10) | varchar(10) | OK |
| 2 | DsEmpDest | varchar(10) | DT_STR(10) | varchar(10) | OK |
| 3 | nrdoctofiscal | int | DT_I4 | int | OK |
| 4 | Previsao de Entrega | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data armazenada como string |
| 5 | ValorCTE | money | DT_CY | money | OK |
| 6 | Data de Emissao CTE | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data armazenada como string |
| 7 | CdRemetente | varchar(14) | DT_STR(14) | varchar(14) | OK (CNPJ como char) |
| 8 | DsRemetente | varchar(100) | DT_STR(100) | varchar(100) | OK |
| 9 | CdDestinatario | varchar(14) | DT_STR(14) | varchar(14) | OK |
| 10 | dsDestinatario | varchar(100) | DT_STR(100) | varchar(100) | OK |
| 11 | CdInscricao | varchar(14) | DT_STR(14) | varchar(14) | OK |
| 12 | dsPagador | varchar(100) | DT_STR(100) | varchar(100) | OK |
| 13 | CidadeColeta | varchar(30) | DT_STR(30) | varchar(30) | OK |
| 14 | DsUFOrigem | varchar(2) | DT_STR(2) | varchar(2) | OK |
| 15 | CidadeEntrega | varchar(30) | DT_STR(30) | varchar(30) | OK |
| 16 | DsUFDestino | varchar(2) | DT_STR(2) | varchar(2) | OK |
| 17 | Peso | money | DT_CY | money | OK |
| 18 | Valor Mercadoria | money | DT_CY | money | OK |
| 19 | Volume | money | DT_CY | money | OK (tipo questionavel para volume) |
| 20 | NrNotaFiscal | varchar(255) | DT_STR(255) | varchar(255) | OK (concatenado) |
| 21 | DsItinerario | varchar(7) | DT_STR(7) | varchar(7) | OK |
| 22 | PlacaManifInterior | varchar(8) | DT_STR(8) | varchar(8) | OK |
| 23 | Motorista Manifesto | varchar(1) | DT_STR(1) | varchar(1) | FLAG 1 char — semantica obscura |
| 24 | DtSaidaParceiro | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data como string |
| 25 | DtSaidachegadadestino | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data como string |
| 26 | NrRomaneio | varchar(16) | DT_STR(16) | varchar(16) | OK |
| 27 | Motorista romaneio | varchar(1) | DT_STR(1) | varchar(1) | FLAG 1 char |
| 28 | DataRomaneio | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data como string |
| 29 | DsUltimaOcorrencia | varchar(50) | DT_STR(50) | varchar(50) | OK |
| 30 | DtUltimaOcorrencia | varchar(30) | DT_STR(30) | varchar(30) | PROBLEMA: data como string |

**Campos de data armazenados como string: 6 colunas** (Previsao de Entrega, Data de Emissao CTE, DtSaidaParceiro, DtSaidachegadadestino, DataRomaneio, DtUltimaOcorrencia)
