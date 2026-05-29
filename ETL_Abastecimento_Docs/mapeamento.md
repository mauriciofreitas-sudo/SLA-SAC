# Mapeamento de Colunas — ETL_Abastecimento

## Visão Geral

```
ORIGEM                                    DESTINO
169.57.181.231                            10.100.86.89
softran_translute                         DBStage

5 fluxos independentes · 75 colunas no total · passthrough direto
```

---

## Fluxo 1 — vwrealabastecimento → stg_Abastecimento (27 colunas)

| # | Coluna (Origem = Destino) | Tipo SSIS | Tam. | Grupo |
|:---:|---|---|:---:|---|
| 1 | `Cdconta` | `DT_I4` | — | Conta / Empresa |
| 2 | `CdVeiculo` | `DT_I4` | — | Veículo |
| 3 | `NrPlaca` | `DT_STR` | 8 | Veículo |
| 4 | `CdEmpresa` | `DT_I4` | — | Conta / Empresa |
| 5 | `DSAPELIDO` | `DT_STR` | 10 | Conta / Empresa |
| 6 | `NrControle` | `DT_I4` | — | Controle |
| 7 | `NrDocumentoExt` | `DT_I4` | — | Controle |
| 8 | `CdInscricao` | `DT_STR` | 14 | Posto / Fornecedor |
| 9 | `DsEntidade` | `DT_STR` | 100 | Posto / Fornecedor |
| 10 | `NrCEP` | `DT_I4` | — | Posto / Localização |
| 11 | `DsBairro` | `DT_STR` | 30 | Posto / Localização |
| 12 | `DsLocal` | `DT_STR` | 30 | Posto / Localização |
| 13 | `DsUF` | `DT_STR` | 2 | Posto / Localização |
| 14 | `DtLancamento` | `DT_DBTIMESTAMP` | — | Data |
| 15 | `CdModelo` | `DT_I4` | — | Modelo / Tipo |
| 16 | `DsModelo` | `DT_STR` | 40 | Modelo / Tipo |
| 17 | `DsAnoModelo` | `DT_I4` | — | Modelo / Tipo |
| 18 | `CdTipoVeiculo` | `DT_I4` | — | Modelo / Tipo |
| 19 | `DsTpVeiculo` | `DT_STR` | 40 | Modelo / Tipo |
| 20 | `DsCombustivel` | `DT_STR` | 40 | Combustível |
| 21 | `VLTotal` | `DT_CY` | — | Valores |
| 22 | `Quantidade` | `DT_CY` | — | Valores |
| 23 | `NrHodometroLanca` | `DT_I4` | — | Hodômetro / KM |
| 24 | `NrHodUltRevisao` | `DT_I4` | — | Hodômetro / KM |
| 25 | `QtMediaApurada` | `DT_CY` | — | Hodômetro / KM |
| 26 | `KM` | `DT_I4` | — | Hodômetro / KM |
| 27 | `TIPO` | `DT_STR` | 8 | Vínculo |

---

## Fluxo 2 — vwrealabastecimento2 → stg_Abastecimento2 (27 colunas)

> Schema **idêntico** ao Fluxo 1. Mesmas 27 colunas, mesmos tipos, mesma ordem.

---

## Fluxo 3 — Abasteciemtos → DescontoAbastecimento (5 colunas)

| # | Coluna (Origem = Destino) | Tipo SSIS | Tam. | Grupo |
|:---:|---|---|:---:|---|
| 1 | `nrplaca` | `DT_STR` | 8 | Veículo |
| 2 | `vlevento` | `DT_NUMERIC` | 14,4 | Valor |
| 3 | `Dtemissao` | `DT_STR` | 30 | Data (como texto) |
| 4 | `cdempresa` | `DT_I4` | — | Empresa |
| 5 | `cdcartafrete` | `DT_I4` | — | Carta de Frete |

---

## Fluxo 4 — TipoVeiculo → Stg_PlacaAg_Combustivel (2 colunas)

| # | Coluna (Origem = Destino) | Tipo SSIS | Tam. | Grupo |
|:---:|---|---|:---:|---|
| 1 | `nrplaca` | `DT_STR` | 8 | Veículo |
| 2 | `TIPO` | `DT_STR` | 1 | Classificação |

---

## Fluxo 5 — vwRazaoVeiculo → stg_RazaoVeiculo (14 colunas)

| # | Coluna (Origem = Destino) | Tipo SSIS | Tam. | Grupo |
|:---:|---|---|:---:|---|
| 1 | `CdVeiculo` | `DT_I4` | — | Veículo |
| 2 | `NrPlaca` | `DT_STR` | 8 | Veículo |
| 3 | `Tpveiculo` | `DT_I4` | — | Tipo |
| 4 | `TipoDeVeiculo` | `DT_STR` | 40 | Tipo |
| 5 | `DtLancamento` | `DT_STR` | 30 | Data (como texto) |
| 6 | `DsConta` | `DT_STR` | 40 | Conta Contábil |
| 7 | `ValorTotal` | `DT_CY` | — | Valor |
| 8 | `CreditoDebito` | `DT_STR` | 1 | Valor |
| 9 | `NrControle` | `DT_I4` | — | Controle |
| 10 | `CdConta` | `DT_I4` | — | Conta Contábil |
| 11 | `CdSequencia` | `DT_I4` | — | Controle |
| 12 | `NrContaContabil` | `DT_I4` | — | Conta Contábil |
| 13 | `DsTpDocumento` | `DT_STR` | 40 | Documento |
| 14 | `DsApelido` | `DT_STR` | 10 | Empresa |

---

## Totais por Tipo de Dado

| Tipo SSIS | SQL Server | Total colunas |
|---|---|:---:|
| `DT_I4` | `int` | 31 |
| `DT_STR` | `varchar` (codepage 1252) | 33 |
| `DT_DBTIMESTAMP` | `datetime` | 1 |
| `DT_CY` | `money` | 7 |
| `DT_NUMERIC(14,4)` | `decimal(14,4)` | 1 |
| **Total** | | **73** |

> Nota: Fluxos 1 e 2 são idênticos — 27 colunas cada. Total lógico = 27+27+5+2+14 = **75**, mas como os schemas são idênticos nos fluxos 1 e 2, a tabela acima conta as colunas únicas **por tipo**.

---

## Distribuição por Fluxo

```
Fluxo 1 (stg_Abastecimento)       ███████████████████████████  27 colunas
Fluxo 2 (stg_Abastecimento2)      ███████████████████████████  27 colunas
Fluxo 3 (DescontoAbastecimento)   █████                         5 colunas
Fluxo 4 (Stg_PlacaAg_Combustivel) ██                            2 colunas
Fluxo 5 (stg_RazaoVeiculo)        ██████████████               14 colunas
                                  ─────────────────────────────
TOTAL                              75 colunas (em 5 tabelas staging)
```
