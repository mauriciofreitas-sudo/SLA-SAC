# Mapeamento de Colunas — ETL_Auditoria_CTe

## Visao Geral

| Data Flow | Source | Destination | Colunas Source | Colunas Destino | Transformacoes |
|-----------|--------|-------------|----------------|-----------------|----------------|
| Fluxo 1 | vw_Auditoria_CTe_Michelin | stg_Auditoria_CTe | 79 | 79 | Nenhuma |
| Fluxo 2 | vw_AuditoriaCTe_Prod | Auditoria_CTe_Cod_Prod | 5 | 4 (1 nao mapeada) | Nenhuma |
| Fluxo 3 | vw_AuditoriaCTe_Prod_v2 | Auditoria_CTe_Cod_Prod_v2 | 6 | 6 | Nenhuma |
| **Total** | | | **90** | **89** | |

---

## Mapeamento Completo — Fluxo 1: stg_Auditoria_CTe

| # | Coluna | Tipo SSIS | Tamanho | Grupo |
|---|--------|-----------|---------|-------|
| 1 | CdPercursoComercial | i4 | — | Identificacao |
| 2 | cdempresa | i4 | — | Identificacao |
| 3 | DsApelidoEmitente | str | 10 | Empresa |
| 4 | CEPColeta | i4 | — | Logistica |
| 5 | dsusuario | str | 10 | Usuario |
| 6 | CEPDestino | i4 | — | Logistica |
| 7 | dsplacaveiculo | str | 8 | Veiculo |
| 8 | NrPlacaReboque1 | str | 8 | Veiculo |
| 9 | DsMotorista | str | 40 | Veiculo |
| 10 | VlFretePeso | numeric | p=14,s=4 | Financeiro |
| 11 | VlFreteValor | numeric | p=14,s=4 | Financeiro |
| 12 | VlPedagio | numeric | p=14,s=4 | Financeiro |
| 13 | VlGRIS | numeric | p=14,s=4 | Financeiro |
| 14 | TotalPrestacao | numeric | p=14,s=4 | Financeiro |
| 15 | nrdoctofiscal | i4 | — | Documento |
| 16 | CdTpDoctoFiscal | i4 | — | Documento |
| 17 | dsapelido | str | 10 | Empresa |
| 18 | DtEmissao | dbTimeStamp | — | Temporal |
| 19 | MesAtual | i4 | — | Temporal |
| 20 | Natureza | str | 40 | Natureza |
| 21 | CodNatureza | i4 | — | Natureza |
| 22 | DsSituacao | str | 9 | Status |
| 23 | DsTpFrete | str | 1 | Frete |
| 24 | DsTipoEmissao | str | 16 | Documento |
| 25 | ParametroCalculo | str | 9 | Calculo |
| 26 | DsUFOrigem | str | 2 | Logistica |
| 27 | DsUFDestino | str | 2 | Logistica |
| 28 | dtentrega | dbTimeStamp | — | Temporal |
| 29 | DtEmissaoFatura | dbTimeStamp | — | Temporal |
| 30 | dtvencimento | dbTimeStamp | — | Temporal |
| 31 | QtPeso | numeric | p=14,s=4 | Carga |
| 32 | QtMetrosCubicos | numeric | p=14,s=4 | Carga |
| 33 | VlMercadoria | numeric | p=14,s=4 | Financeiro |
| 34 | VlTotalPrestacao | numeric | p=14,s=4 | Financeiro |
| 35 | VlLiquido | numeric | p=14,s=4 | Financeiro |
| 36 | VlICMS | numeric | p=14,s=4 | Financeiro |
| 37 | QtVolume | numeric | p=14,s=4 | Carga |
| 38 | CdRemetente | str | 14 | Participantes |
| 39 | DsRemetente | str | 100 | Participantes |
| 40 | CdDestinatario | str | 14 | Participantes |
| 41 | dsDestinatario | str | 100 | Participantes |
| 42 | CdConsignatario | str | 14 | Participantes |
| 43 | dsConsignatario | str | 100 | Participantes |
| 44 | CdInscricao | str | 14 | Participantes |
| 45 | dsPagador | str | 100 | Participantes |
| 46 | CdEmpresaDestino | i4 | — | Empresa |
| 47 | DsEmpDest | str | 10 | Empresa |
| 48 | CidadeColeta | str | 30 | Logistica |
| 49 | CdRegiaoColeta | i4 | — | Logistica |
| 50 | CidadeEntrega | str | 30 | Logistica |
| 51 | CdRegiaoEntrega | i4 | — | Logistica |
| 52 | Vlcad | numeric | p=14,s=4 | Financeiro |
| 53 | VlTRT | numeric | p=14,s=4 | Financeiro |
| 54 | cdfatura | i4 | — | Documento |
| 55 | InTDE | str | 3 | Indicadores |
| 56 | DoctoOrigem | i4 | — | Documento |
| 57 | NrNotaFiscal | str | 255 | Documento |
| 58 | DsSituacaoArq | str | 13 | Status |
| 59 | VlFreteOrigem | numeric | p=14,s=4 | Financeiro |
| 60 | VlICMSOrigem | numeric | p=14,s=4 | Financeiro |
| 61 | dsnatureza | str | 40 | Natureza |
| 62 | dstransporte | str | 40 | Empresa |
| 63 | DsUltimaOcorrencia | str | 50 | Ocorrencia |
| 64 | DtUltimaOcorrencia | dbTimeStamp | — | Temporal |
| 65 | DataRomaneio | dbTimeStamp | — | Temporal |
| 66 | NrRomaneio | str | 16 | Romaneio |
| 67 | NrPlacaRomaneio | str | 8 | Veiculo |
| 68 | PlacaManifInterior | str | 8 | Veiculo |
| 69 | DtSaidaParceiro | dbTimeStamp | — | Temporal |
| 70 | DsItinerario | str | 7 | Logistica |
| 71 | ArquivoConemb | str | 255 | EDI |
| 72 | DtEnvioConemb | dbTimeStamp | — | EDI |
| 73 | ArquivoOcoren | str | 255 | EDI |
| 74 | DtEnvioOcoren | dbTimeStamp | — | EDI |
| 75 | ArquivoDocCob | str | 255 | EDI |
| 76 | DtEnvioDocCob | dbTimeStamp | — | EDI |
| 77 | Pedido | str | 30 | Documento |
| 78 | Conteudo | str | 111 | Documento |
| 79 | StatusSubstituido | str | 3 | Status |

---

## Mapeamento Completo — Fluxo 2: Auditoria_CTe_Cod_Prod

| # | Coluna | Tipo SSIS | Tamanho | Mapeado |
|---|--------|-----------|---------|---------|
| 1 | EmpresaNF | i4 | — | Sim |
| 2 | DoctFiscalNF | i4 | — | Sim |
| 3 | TpdoctfiscalNF | i4 | — | Sim |
| 4 | CdNaturezaNF | i4 | — | Sim |
| 5 | CdProdCliente | (str) | — | Nao (presente na source, ausente no destino) |

---

## Mapeamento Completo — Fluxo 3: Auditoria_CTe_Cod_Prod_v2

| # | Coluna | Tipo SSIS | Tamanho | Mapeado |
|---|--------|-----------|---------|---------|
| 1 | CdProduto | str | 25 | Sim |
| 2 | DsNatureza | str | 40 | Sim |
| 3 | CdNatureza | i4 | — | Sim |
| 4 | EmpresaNF | i4 | — | Sim |
| 5 | DoctFiscalNF | i4 | — | Sim |
| 6 | TpdoctfiscalNF | i4 | — | Sim |

---

## Distribuicao por Grupo — Fluxo Principal (stg_Auditoria_CTe)

```
Financeiro    [##################] 15 colunas (19%)
Temporal      [##########]         10 colunas (13%)
Documento     [##########]          9 colunas (11%)
Logistica     [##########]          9 colunas (11%)
Participantes [########]            8 colunas (10%)
Veiculo       [#####]               5 colunas (6%)
Empresa       [#####]               5 colunas (6%)
Status        [###]                 3 colunas (4%)
Natureza      [###]                 3 colunas (4%)
EDI           [######]              6 colunas (8%)
Carga         [###]                 3 colunas (4%)
Outros        [##]                  3 colunas (4%)
                                    Total: 79 colunas
```

---

## Tipos de Dados — Resumo (Fluxo Principal)

| Tipo SSIS | Equivalente SQL Server | Quantidade |
|-----------|----------------------|------------|
| str (cp1252) | VARCHAR / CHAR | 42 colunas |
| numeric(14,4) | DECIMAL(14,4) | 17 colunas |
| dbTimeStamp | DATETIME | 12 colunas |
| i4 | INT | 8 colunas |
| **Total** | | **79** |
