# Destino dos Dados — ETL_Abastecimento

## Servidor de Destino

| Atributo | Valor |
|---|---|
| **Servidor** | `10.100.86.89` |
| **Banco de dados** | `DBStage` ← staging, **não** DWGrupolc |
| **Usuário de conexão** | `sqldba` |
| **Tipo de conexão** | OLE DB (`SQLOLEDB.1`) |
| **Modo de inserção** | Fast Load — `TABLOCK, CHECK_CONSTRAINTS` |
| **CommandTimeout** | `0` (ilimitado) |

> Este package carrega para a camada de **staging** (`DBStage`). Uma segunda etapa (não documentada aqui) deve transformar e mover os dados para o DW final (`DWGrupolc`).

---

## Tabelas de Destino

### DESTINO 1 — stg_Abastecimento

| Atributo | Valor |
|---|---|
| **Tabela** | `[dbo].[stg_Abastecimento]` |
| **Banco** | DBStage · 10.100.86.89 |
| **Origem** | `dbo.vwrealabastecimento` |
| **Colunas carregadas** | 27 |
| **Estratégia** | DELETE + INSERT (full-refresh) |

**Schema:**

| Coluna | Tipo SSIS | Tam. |
|---|---|:---:|
| `Cdconta` | i4 (int) | — |
| `CdVeiculo` | i4 (int) | — |
| `NrPlaca` | str (varchar) | 8 |
| `CdEmpresa` | i4 (int) | — |
| `DSAPELIDO` | str (varchar) | 10 |
| `NrControle` | i4 (int) | — |
| `NrDocumentoExt` | i4 (int) | — |
| `CdInscricao` | str (varchar) | 14 |
| `DsEntidade` | str (varchar) | 100 |
| `NrCEP` | i4 (int) | — |
| `DsBairro` | str (varchar) | 30 |
| `DsLocal` | str (varchar) | 30 |
| `DsUF` | str (varchar) | 2 |
| `DtLancamento` | dbTimeStamp (datetime) | — |
| `CdModelo` | i4 (int) | — |
| `DsModelo` | str (varchar) | 40 |
| `DsAnoModelo` | i4 (int) | — |
| `CdTipoVeiculo` | i4 (int) | — |
| `DsTpVeiculo` | str (varchar) | 40 |
| `DsCombustivel` | str (varchar) | 40 |
| `VLTotal` | cy (money) | — |
| `Quantidade` | cy (money) | — |
| `NrHodometroLanca` | i4 (int) | — |
| `NrHodUltRevisao` | i4 (int) | — |
| `QtMediaApurada` | cy (money) | — |
| `KM` | i4 (int) | — |
| `TIPO` | str (varchar) | 8 |

---

### DESTINO 2 — stg_Abastecimento2

| Atributo | Valor |
|---|---|
| **Tabela** | `[dbo].[stg_Abastecimento2]` |
| **Banco** | DBStage · 10.100.86.89 |
| **Origem** | `dbo.vwrealabastecimento2` |
| **Colunas carregadas** | 27 |
| **Estratégia** | DELETE + INSERT (full-refresh) |

> Schema idêntico ao `stg_Abastecimento` (mesmas 27 colunas, mesmos tipos).

---

### DESTINO 3 — DescontoAbastecimento

| Atributo | Valor |
|---|---|
| **Tabela** | `[dbo].[DescontoAbastecimento]` |
| **Banco** | DBStage · 10.100.86.89 |
| **Origem** | `dbo.Abasteciemtos` ⚠️ (typo) |
| **Colunas carregadas** | 5 |
| **Estratégia** | DELETE + INSERT (full-refresh) |

**Schema:**

| Coluna | Tipo SSIS | Tam. |
|---|---|:---:|
| `nrplaca` | str (varchar) | 8 |
| `vlevento` | numeric | 14,4 |
| `Dtemissao` | str (varchar) | 30 |
| `cdempresa` | i4 (int) | — |
| `cdcartafrete` | i4 (int) | — |

---

### DESTINO 4 — Stg_PlacaAg_Combustivel

| Atributo | Valor |
|---|---|
| **Tabela** | `[dbo].[Stg_PlacaAg_Combustivel]` |
| **Banco** | DBStage · 10.100.86.89 |
| **Origem** | `dbo.TipoVeiculo` |
| **Colunas carregadas** | 2 |
| **Estratégia** | DELETE + INSERT (full-refresh) |

**Schema:**

| Coluna | Tipo SSIS | Tam. |
|---|---|:---:|
| `nrplaca` | str (varchar) | 8 |
| `TIPO` | str (varchar) | 1 |

---

### DESTINO 5 — stg_RazaoVeiculo

| Atributo | Valor |
|---|---|
| **Tabela** | `[dbo].[stg_RazaoVeiculo]` |
| **Banco** | DBStage · 10.100.86.89 |
| **Origem** | `dbo.vwRazaoVeiculo` |
| **Colunas carregadas** | 14 |
| **Estratégia** | DELETE + INSERT (full-refresh) |

**Schema:**

| Coluna | Tipo SSIS | Tam. |
|---|---|:---:|
| `CdVeiculo` | i4 (int) | — |
| `NrPlaca` | str (varchar) | 8 |
| `Tpveiculo` | i4 (int) | — |
| `TipoDeVeiculo` | str (varchar) | 40 |
| `DtLancamento` | str (varchar) | 30 |
| `DsConta` | str (varchar) | 40 |
| `ValorTotal` | cy (money) | — |
| `CreditoDebito` | str (varchar) | 1 |
| `NrControle` | i4 (int) | — |
| `CdConta` | i4 (int) | — |
| `CdSequencia` | i4 (int) | — |
| `NrContaContabil` | i4 (int) | — |
| `DsTpDocumento` | str (varchar) | 40 |
| `DsApelido` | str (varchar) | 10 |

---

## Configurações de Performance (Fast Load)

| Configuração | Valor |
|---|---|
| `AccessMode` | 3 (Fast Load) |
| `FastLoadOptions` | `TABLOCK, CHECK_CONSTRAINTS` |
| `FastLoadKeepIdentity` | false |
| `FastLoadKeepNulls` | false |
| `FastLoadMaxInsertCommitSize` | 2147483647 (commit único no final) |
| `CommandTimeout` | 0 (sem timeout — ilimitado) |

> `TABLOCK` bloqueia a tabela inteira durante a carga — garante performance mas impede leitura concorrente. Adequado para staging onde não há usuários consultando.
