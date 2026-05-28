# Mapeamento de Colunas — ETL_Fichas

## Visão Geral

```
ORIGEM                              DESTINO
169.57.181.231                      10.100.86.89
SOFTRAN_TRANSLUTE                   DWGrupolc
dbo.Ficha_de_Viagem_Geral (view)    dbo.fFichaViagem (tabela)

51 colunas mapeadas  ────────────►  51 colunas carregadas
                                    + 40 colunas sem dados (NULL)
                                    = 91 colunas no schema total
```

---

## Mapeamento Completo — 51 Colunas

Todas as colunas são passthrough direto: sem renomeação, sem conversão de tipo, sem transformação.

| # | Coluna (Origem = Destino) | Tipo SSIS | Tamanho | Grupo |
|:---:|---|---|:---:|---|
| 1 | `EmpresaFicha` | `DT_I4` (int) | — | Ficha de Viagem |
| 2 | `NrFicha` | `DT_I4` (int) | — | Ficha de Viagem |
| 3 | `DtEmissao` | `DT_DBTIMESTAMP` (datetime) | — | Ficha de Viagem |
| 4 | `VlComissao` | `DT_NUMERIC` | 14,4 | Ficha de Viagem |
| 5 | `CdRotaRMS` | `DT_I4` (int) | — | Ficha de Viagem |
| 6 | `MotoristaFV` | `DT_WSTR` (nvarchar) | 100 | Ficha de Viagem |
| 7 | `ProprietarioFV` | `DT_WSTR` (nvarchar) | 100 | Ficha de Viagem |
| 8 | `VeiculoFV` | `DT_WSTR` (nvarchar) | 8 | Ficha de Viagem |
| 9 | `CarretaFV` | `DT_WSTR` (nvarchar) | 8 | Ficha de Viagem |
| 10 | `Carreta2FV` | `DT_WSTR` (nvarchar) | 8 | Ficha de Viagem |
| 11 | `Carreta3FV` | `DT_WSTR` (nvarchar) | 8 | Ficha de Viagem |
| 12 | `UserFicha` | `DT_WSTR` (nvarchar) | 10 | Ficha de Viagem |
| 13 | `EmpresaRomaneio` | `DT_I4` (int) | — | Romaneio/Manifesto |
| 14 | `RotaRomaneio` | `DT_I4` (int) | — | Romaneio/Manifesto |
| 15 | `RomaneioManifesto` | `DT_WSTR` (nvarchar) | 20 | Romaneio/Manifesto |
| 16 | `UserRomaneioManifesto` | `DT_WSTR` (nvarchar) | 10 | Romaneio/Manifesto |
| 17 | `DtEmissaoRmMan` | `DT_DBTIMESTAMP` (datetime) | — | Romaneio/Manifesto |
| 18 | `VeiculoRmMan` | `DT_WSTR` (nvarchar) | 8 | Romaneio/Manifesto |
| 19 | `CarretaRmMan` | `DT_WSTR` (nvarchar) | 8 | Romaneio/Manifesto |
| 20 | `Carreta2RmMan` | `DT_WSTR` (nvarchar) | 8 | Romaneio/Manifesto |
| 21 | `Carreta3RmMan` | `DT_WSTR` (nvarchar) | 8 | Romaneio/Manifesto |
| 22 | `MotoristaRmMan` | `DT_WSTR` (nvarchar) | 40 | Romaneio/Manifesto |
| 23 | `ProprietarioRmMan` | `DT_WSTR` (nvarchar) | 40 | Romaneio/Manifesto |
| 24 | `NumeroCTRB` | `DT_I4` (int) | — | CTRB |
| 25 | `UserCtrb` | `DT_WSTR` (nvarchar) | 10 | CTRB |
| 26 | `DtEmissaoCTRB` | `DT_DBTIMESTAMP` (datetime) | — | CTRB |
| 27 | `ValorTotalCTRB` | `DT_NUMERIC` | 14,4 | CTRB |
| 28 | `AdtoCTRB` | `DT_NUMERIC` | 14,4 | CTRB |
| 29 | `PedagioCTRB` | `DT_NUMERIC` | 14,4 | CTRB |
| 30 | `EmpresaCTE` | `DT_I4` (int) | — | CT-e |
| 31 | `NrDoctoFiscal` | `DT_I4` (int) | — | CT-e |
| 32 | `UserCTE` | `DT_WSTR` (nvarchar) | 10 | CT-e |
| 33 | `TipoDoctoFiscal` | `DT_WSTR` (nvarchar) | 10 | CT-e |
| 34 | `DtEmissaoCTE` | `DT_DBTIMESTAMP` (datetime) | — | CT-e |
| 35 | `VlFretePeso` | `DT_NUMERIC` | 14,4 | Valores de Frete |
| 36 | `VlFreteValor` | `DT_NUMERIC` | 14,4 | Valores de Frete |
| 37 | `VlPedagio` | `DT_NUMERIC` | 14,4 | Valores de Frete |
| 38 | `VlGRIS` | `DT_NUMERIC` | 14,4 | Valores de Frete |
| 39 | `VlTotalPrestacao` | `DT_NUMERIC` | 14,4 | Valores de Frete |
| 40 | `CidadeColeta` | `DT_WSTR` (nvarchar) | 30 | Localização |
| 41 | `DsUFOrigem` | `DT_WSTR` (nvarchar) | 2 | Localização |
| 42 | `CidadeEntrega` | `DT_WSTR` (nvarchar) | 30 | Localização |
| 43 | `DsUFDestino` | `DT_WSTR` (nvarchar) | 2 | Localização |
| 44 | `CdRemetente` | `DT_WSTR` (nvarchar) | 14 | Partes Envolvidas |
| 45 | `DsRemetente` | `DT_WSTR` (nvarchar) | 100 | Partes Envolvidas |
| 46 | `CdDestinatario` | `DT_WSTR` (nvarchar) | 14 | Partes Envolvidas |
| 47 | `DsDestinatario` | `DT_WSTR` (nvarchar) | 100 | Partes Envolvidas |
| 48 | `CdInscricao` | `DT_WSTR` (nvarchar) | 14 | Partes Envolvidas |
| 49 | `DsPagador` | `DT_WSTR` (nvarchar) | 100 | Partes Envolvidas |
| 50 | `CdRedespacho` | `DT_WSTR` (nvarchar) | 14 | Partes Envolvidas |
| 51 | `DsRedespacho` | `DT_WSTR` (nvarchar) | 40 | Partes Envolvidas |

---

## Colunas sem Mapeamento — 40 colunas (ficarão NULL)

| # | Coluna no Destino | Tipo | Tamanho |
|:---:|---|---|:---:|
| 52 | `NrDiasAtraso` | int | — |
| 53 | `Status` | nvarchar | 14 |
| 54 | `DtPrevisaoEntregaCTeNova` | datetime | — |
| 55 | `FichaViagem` | nvarchar | 32 |
| 56 | `Hora` | datetime | — |
| 57 | `IDCTEPERF` | nvarchar | 42 |
| 58 | `CNPJRemetente` | nvarchar | 14 |
| 59 | `NomeRemetente` | nvarchar | 100 |
| 60 | `NFSerie` | nvarchar | 3 |
| 61 | `NFNr` | int | — |
| 62 | `NFVlr` | numeric | 14,4 |
| 63 | `NFDtEmissao` | datetime | — |
| 64 | `CTeEmpresa` | int | — |
| 65 | `CTeNr` | int | — |
| 66 | `CTeDtEmissao` | datetime | — |
| 67 | `DtPrevisaoEntregaCTe` | datetime | — |
| 68 | `DtAgendamentoEntregaCTe` | datetime | — |
| 69 | `CTeDtEntrega` | datetime | — |
| 70 | `CNPJDestinatario` | nvarchar | 14 |
| 71 | `NomeDestinatario` | nvarchar | 100 |
| 72 | `CidadeDestino` | nvarchar | 30 |
| 73 | `UFDestino` | nvarchar | 2 |
| 74 | `CNPJPagador` | nvarchar | 14 |
| 75 | `NomePagador` | nvarchar | 100 |
| 76 | `NrSeqControle` | int | — |
| 77 | `NaturezaCod` | int | — |
| 78 | `NaturezaNome` | nvarchar | 40 |
| 79 | `TipoTransporte` | nvarchar | 40 |
| 80 | `Volumes` | numeric | 14,4 |
| 81 | `QtPesoCubado` | numeric | 14,4 |
| 82 | `HistEntregaCod` | int | — |
| 83 | `HistEntregaNome` | nvarchar | 50 |
| 84 | `FichaVUltManifesto` | nvarchar | 21 |
| 85 | `FichaVUltRom` | nvarchar | 32 |
| 86 | `DtRomaneio` | datetime | — |
| 87 | `DtManifesto` | datetime | — |
| 88 | `TipoEmissao` | nvarchar | 16 |
| 89 | `DsGrupoCliente` | nvarchar | 100 |
| 90 | `CdGrupoCliente` | int | — |
| 91 | `NrRegistros` | int | — |

---

## Distribuição por Grupo

```
Ficha de Viagem      ████████████  12 colunas
Romaneio/Manifesto   ███████████   11 colunas
CTRB                 ██████         6 colunas
CT-e / Doc. Fiscal   █████          5 colunas
Valores de Frete     █████          5 colunas
Localização          ████           4 colunas
Partes Envolvidas    ████████       8 colunas
                     ──────────────────────────
TOTAL MAPEADO        51 colunas

Não mapeadas         ████████████████████████  40 colunas
                     ──────────────────────────
TOTAL NO DESTINO     91 colunas
```

---

## Tipos de Dados por Categoria

| Tipo SSIS | Equivalente SQL | Quantidade | Colunas |
|---|---|:---:|---|
| `DT_I4` | `int` | 14 | EmpresaFicha, NrFicha, CdRotaRMS, EmpresaRomaneio, RotaRomaneio, NumeroCTRB, EmpresaCTE, NrDoctoFiscal |
| `DT_DBTIMESTAMP` | `datetime` | 7 | DtEmissao, DtEmissaoRmMan, DtEmissaoCTRB, DtEmissaoCTE + 3 |
| `DT_NUMERIC(14,4)` | `numeric(14,4)` | 10 | VlComissao, ValorTotalCTRB, AdtoCTRB, PedagioCTRB, VlFretePeso, VlFreteValor, VlPedagio, VlGRIS, VlTotalPrestacao |
| `DT_WSTR` | `nvarchar` | 20 | Todos os campos de texto |
