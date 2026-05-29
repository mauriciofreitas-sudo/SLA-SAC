# ETL_Mapeamento_Carga — Mapeamento Completo

## Visao Geral

| Aspecto | Valor |
|---|---|
| Servidor Origem | 169.57.181.231 (SOFTRAN_TRANSLUTE) |
| Servidor Destino | 10.100.86.89 (DBStage) |
| Tabela Origem | [dbo].[MapeamentodeCargas] |
| Tabela Destino | [dbo].[tblBasePrincipal] |
| Total de Colunas | 30 |
| Fluxos de Dados | 1 |

## Mapeamento Completo por Fluxo

| # | Coluna | Tipo SSIS | Tamanho | Grupo |
|---|---|---|---|---|
| 1 | DsApelidoEmitente | DT_STR | 10 | Empresa |
| 2 | DsEmpDest | DT_STR | 10 | Empresa |
| 3 | nrdoctofiscal | DT_I4 | — | Fiscal |
| 4 | Previsao de Entrega | DT_STR | 30 | Data (como string) |
| 5 | ValorCTE | DT_CY | — | Financeiro |
| 6 | Data de Emissao CTE | DT_STR | 30 | Data (como string) |
| 7 | CdRemetente | DT_STR | 14 | Remetente |
| 8 | DsRemetente | DT_STR | 100 | Remetente |
| 9 | CdDestinatario | DT_STR | 14 | Destinatario |
| 10 | dsDestinatario | DT_STR | 100 | Destinatario |
| 11 | CdInscricao | DT_STR | 14 | Pagador |
| 12 | dsPagador | DT_STR | 100 | Pagador |
| 13 | CidadeColeta | DT_STR | 30 | Localizacao |
| 14 | DsUFOrigem | DT_STR | 2 | Localizacao |
| 15 | CidadeEntrega | DT_STR | 30 | Localizacao |
| 16 | DsUFDestino | DT_STR | 2 | Localizacao |
| 17 | Peso | DT_CY | — | Fisico |
| 18 | Valor Mercadoria | DT_CY | — | Financeiro |
| 19 | Volume | DT_CY | — | Fisico |
| 20 | NrNotaFiscal | DT_STR | 255 | Fiscal |
| 21 | DsItinerario | DT_STR | 7 | Logistica |
| 22 | PlacaManifInterior | DT_STR | 8 | Logistica |
| 23 | Motorista Manifesto | DT_STR | 1 | Logistica |
| 24 | DtSaidaParceiro | DT_STR | 30 | Data (como string) |
| 25 | DtSaidachegadadestino | DT_STR | 30 | Data (como string) |
| 26 | NrRomaneio | DT_STR | 16 | Logistica |
| 27 | Motorista romaneio | DT_STR | 1 | Logistica |
| 28 | DataRomaneio | DT_STR | 30 | Data (como string) |
| 29 | DsUltimaOcorrencia | DT_STR | 50 | Ocorrencia |
| 30 | DtUltimaOcorrencia | DT_STR | 30 | Data (como string) |

## Distribuicao por Grupo

```
Empresa          [==========] 2 cols  (7%)
Fiscal           [==========] 2 cols  (7%)
Remetente        [====================] 2 cols  (7%)
Destinatario     [====================] 2 cols  (7%)
Pagador          [====================] 2 cols  (7%)
Localizacao      [========================================] 4 cols  (13%)
Financeiro       [====================] 2 cols  (7%)
Fisico           [====================] 2 cols  (7%)
Logistica        [================================================] 5 cols  (17%)
Data (string)    [============================================================] 6 cols  (20%)
Ocorrencia       [==========] 1 col   (3%)
```

## Totais por Tipo de Dado

| Tipo SSIS | Quantidade | Percentual |
|---|---|---|
| DT_STR (varchar/ANSI) | 27 | 90% |
| DT_I4 (integer) | 1 | 3% |
| DT_CY (currency/money) | 3 | 10% |
| **Total** | **30** | **100%** |

> Nenhum campo DT_DBTIMESTAMP — todas as datas sao strings (problema de design).
