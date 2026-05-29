# Origem dos Dados — ETL_Auditoria_CTe

## Identificação do Servidor de Origem

| Propriedade | Valor |
|-------------|-------|
| Servidor | 169.57.181.231 |
| Banco de Dados | softran_translute |
| Usuario | softran |
| Tipo de Conexao | OLEDB — SQLOLEDB.1 |
| Connection Manager | SOFTRAN - TRANSLUTE |
| DefaultCodePage | 1252 (Latin1) |

---

## Source 1: `Origem Auditoria Cte`

### Configuracao

| Propriedade | Valor |
|-------------|-------|
| Componente | OLE DB Source |
| SQL Command | `select * from [dbo].[vw_Auditoria_CTe_Michelin] with (nolock)` |
| AccessMode | 2 (SqlCommand) |
| CommandTimeout | 300 segundos |

### Colunas Extraídas — `vw_Auditoria_CTe_Michelin`

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Grupo |
|---|--------|-----------|-----------------|-------|
| 1 | CdPercursoComercial | i4 | — | Identificacao |
| 2 | cdempresa | i4 | — | Identificacao |
| 3 | DsApelidoEmitente | str (cp1252) | 10 | Empresa |
| 4 | CEPColeta | i4 | — | Logistica |
| 5 | dsusuario | str (cp1252) | 10 | Usuario |
| 6 | CEPDestino | i4 | — | Logistica |
| 7 | dsplacaveiculo | str (cp1252) | 8 | Veiculo |
| 8 | NrPlacaReboque1 | str (cp1252) | 8 | Veiculo |
| 9 | DsMotorista | str (cp1252) | 40 | Veiculo |
| 10 | VlFretePeso | numeric | p=14, s=4 | Financeiro |
| 11 | VlFreteValor | numeric | p=14, s=4 | Financeiro |
| 12 | VlPedagio | numeric | p=14, s=4 | Financeiro |
| 13 | VlGRIS | numeric | p=14, s=4 | Financeiro |
| 14 | TotalPrestacao | numeric | p=14, s=4 | Financeiro |
| 15 | nrdoctofiscal | i4 | — | Documento |
| 16 | CdTpDoctoFiscal | i4 | — | Documento |
| 17 | dsapelido | str (cp1252) | 10 | Empresa |
| 18 | DtEmissao | dbTimeStamp | — | Temporal |
| 19 | MesAtual | i4 | — | Temporal |
| 20 | Natureza | str (cp1252) | 40 | Natureza |
| 21 | CodNatureza | i4 | — | Natureza |
| 22 | DsSituacao | str (cp1252) | 9 | Status |
| 23 | DsTpFrete | str (cp1252) | 1 | Frete |
| 24 | DsTipoEmissao | str (cp1252) | 16 | Documento |
| 25 | ParametroCalculo | str (cp1252) | 9 | Calculo |
| 26 | DsUFOrigem | str (cp1252) | 2 | Logistica |
| 27 | DsUFDestino | str (cp1252) | 2 | Logistica |
| 28 | dtentrega | dbTimeStamp | — | Temporal |
| 29 | DtEmissaoFatura | dbTimeStamp | — | Temporal |
| 30 | dtvencimento | dbTimeStamp | — | Temporal |
| 31 | QtPeso | numeric | p=14, s=4 | Carga |
| 32 | QtMetrosCubicos | numeric | p=14, s=4 | Carga |
| 33 | VlMercadoria | numeric | p=14, s=4 | Financeiro |
| 34 | VlTotalPrestacao | numeric | p=14, s=4 | Financeiro |
| 35 | VlLiquido | numeric | p=14, s=4 | Financeiro |
| 36 | VlICMS | numeric | p=14, s=4 | Financeiro |
| 37 | QtVolume | numeric | p=14, s=4 | Carga |
| 38 | CdRemetente | str (cp1252) | 14 | Participantes |
| 39 | DsRemetente | str (cp1252) | 100 | Participantes |
| 40 | CdDestinatario | str (cp1252) | 14 | Participantes |
| 41 | dsDestinatario | str (cp1252) | 100 | Participantes |
| 42 | CdConsignatario | str (cp1252) | 14 | Participantes |
| 43 | dsConsignatario | str (cp1252) | 100 | Participantes |
| 44 | CdInscricao | str (cp1252) | 14 | Participantes |
| 45 | dsPagador | str (cp1252) | 100 | Participantes |
| 46 | CdEmpresaDestino | i4 | — | Empresa |
| 47 | DsEmpDest | str (cp1252) | 10 | Empresa |
| 48 | CidadeColeta | str (cp1252) | 30 | Logistica |
| 49 | CdRegiaoColeta | i4 | — | Logistica |
| 50 | CidadeEntrega | str (cp1252) | 30 | Logistica |
| 51 | CdRegiaoEntrega | i4 | — | Logistica |
| 52 | Vlcad | numeric | p=14, s=4 | Financeiro |
| 53 | VlTRT | numeric | p=14, s=4 | Financeiro |
| 54 | cdfatura | i4 | — | Documento |
| 55 | InTDE | str (cp1252) | 3 | Indicadores |
| 56 | DoctoOrigem | i4 | — | Documento |
| 57 | NrNotaFiscal | str (cp1252) | 255 | Documento |
| 58 | DsSituacaoArq | str (cp1252) | 13 | Status |
| 59 | VlFreteOrigem | numeric | p=14, s=4 | Financeiro |
| 60 | VlICMSOrigem | numeric | p=14, s=4 | Financeiro |
| 61 | dsnatureza | str (cp1252) | 40 | Natureza |
| 62 | dstransporte | str (cp1252) | 40 | Empresa |
| 63 | DsUltimaOcorrencia | str (cp1252) | 50 | Ocorrencia |
| 64 | DtUltimaOcorrencia | dbTimeStamp | — | Temporal |
| 65 | DataRomaneio | dbTimeStamp | — | Temporal |
| 66 | NrRomaneio | str (cp1252) | 16 | Romaneio |
| 67 | NrPlacaRomaneio | str (cp1252) | 8 | Veiculo |
| 68 | PlacaManifInterior | str (cp1252) | 8 | Veiculo |
| 69 | DtSaidaParceiro | dbTimeStamp | — | Temporal |
| 70 | DsItinerario | str (cp1252) | 7 | Logistica |
| 71 | ArquivoConemb | str (cp1252) | 255 | EDI |
| 72 | DtEnvioConemb | dbTimeStamp | — | EDI |
| 73 | ArquivoOcoren | str (cp1252) | 255 | EDI |
| 74 | DtEnvioOcoren | dbTimeStamp | — | EDI |
| 75 | ArquivoDocCob | str (cp1252) | 255 | EDI |
| 76 | DtEnvioDocCob | dbTimeStamp | — | EDI |
| 77 | Pedido | str (cp1252) | 30 | Documento |
| 78 | Conteudo | str (cp1252) | 111 | Documento |
| 79 | StatusSubstituido | str (cp1252) | 3 | Status |

> **Total: 79 colunas** extraidas da view `vw_Auditoria_CTe_Michelin`. Somente 53 sao mapeadas para o destino (as demais nao aparecem nos inputColumns do destino, mas estao na source output).

---

## Source 2: `Origem Cte Produto`

### Configuracao

| Propriedade | Valor |
|-------------|-------|
| Componente | OLE DB Source |
| SQL Command | `select * from vw_AuditoriaCTe_Prod with (nolock)` |
| AccessMode | 2 (SqlCommand) |
| CommandTimeout | 0 (infinito) |

### Colunas Extraidas — `vw_AuditoriaCTe_Prod`

| # | Coluna | Tipo SSIS | Tamanho | Descricao |
|---|--------|-----------|---------|-----------|
| 1 | EmpresaNF | i4 | — | Codigo da empresa da NF |
| 2 | DoctFiscalNF | i4 | — | Numero do documento fiscal |
| 3 | TpdoctfiscalNF | i4 | — | Tipo do documento fiscal |
| 4 | CdNaturezaNF | i4 | — | Codigo da natureza da NF |
| 5 | CdProdCliente | (visto na source) | — | Codigo do produto do cliente |

---

## Source 3: `Origem Prod_Natureza`

### Configuracao

| Propriedade | Valor |
|-------------|-------|
| Componente | OLE DB Source |
| SQL Command | `select * from [dbo].[vw_AuditoriaCTe_Prod_v2] with (nolock)` |
| AccessMode | 2 (SqlCommand) |
| CommandTimeout | 0 (infinito) |

### Colunas Extraidas — `vw_AuditoriaCTe_Prod_v2`

| # | Coluna | Tipo SSIS | Tamanho | Descricao |
|---|--------|-----------|---------|-----------|
| 1 | CdProduto | str (cp1252) | 25 | Codigo do produto |
| 2 | DsNatureza | str (cp1252) | 40 | Descricao da natureza |
| 3 | CdNatureza | i4 | — | Codigo da natureza |
| 4 | EmpresaNF | i4 | — | Codigo da empresa |
| 5 | DoctFiscalNF | i4 | — | Numero do documento fiscal |
| 6 | TpdoctfiscalNF | i4 | — | Tipo do documento fiscal |
