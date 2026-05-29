# Origem dos Dados — ETL_Abastecimento

## Servidor de Origem

| Atributo | Valor |
|---|---|
| **Servidor** | `169.57.181.231` |
| **Banco de dados** | `softran_translute` |
| **Usuário de conexão** | `softran` |
| **Tipo de conexão** | OLE DB (`SQLOLEDB.1`) |
| **Retry Count** | 1 vez · Retry Interval: 5s |

---

## Origens do Data Flow

### ORIGEM 1 — Abastecimento Principal

| Atributo | Valor |
|---|---|
| **Componente** | `Origem Abastecimento` |
| **Objeto** | `dbo.vwrealabastecimento` |
| **Tipo** | View |
| **Query** | `select * from [dbo].[vwrealabastecimento] with(nolock)` |
| **Destino** | `dbo.stg_Abastecimento` |
| **Colunas** | 27 |

**Descrição**: View principal de abastecimento real — registros de abastecimento de veículos com dados de placa, empresa, combustível, hodômetro, quantidade e valor.

---

### ORIGEM 2 — Abastecimento Complementar (Arla)

| Atributo | Valor |
|---|---|
| **Componente** | `Origem Arla` |
| **Objeto** | `dbo.vwrealabastecimento2` |
| **Tipo** | View |
| **Query** | `select * from [dbo].[vwrealabastecimento2] with (nolock)` |
| **Destino** | `dbo.stg_Abastecimento2` |
| **Colunas** | 27 |

**Descrição**: View secundária de abastecimento — provavelmente inclui abastecimentos de Arla32 (fluido redutor de emissões para veículos diesel Euro 5/6) ou outra categoria de insumo.

---

### ORIGEM 3 — Desconto de Abastecimento

| Atributo | Valor |
|---|---|
| **Componente** | `Origem Comple` |
| **Objeto** | `dbo.Abasteciemtos` ⚠️ |
| **Tipo** | Tabela |
| **Query** | `select* from [dbo].[Abasteciemtos]` ⚠️ |
| **Destino** | `dbo.DescontoAbastecimento` |
| **Colunas** | 5 |

> ⚠️ **Erro de digitação**: o nome da tabela está como `Abasteciemtos` (letra 'e' e 'i' trocadas). O correto seria `Abastecimentos`. O package só funciona se existir uma tabela com exatamente esse nome no banco. Também há ausência de espaço em `select*`.

---

### ORIGEM 4 — Tipo de Veículo / Placa Agregado

| Atributo | Valor |
|---|---|
| **Componente** | `Origem PlacaAgregado` |
| **Objeto** | `dbo.TipoVeiculo` |
| **Tipo** | Tabela |
| **Query** | `select * from TipoVeiculo with (nolock)` |
| **Destino** | `dbo.Stg_PlacaAg_Combustivel` |
| **Colunas** | 2 |

**Descrição**: Tabela de classificação de veículos por tipo (agregado, próprio, terceiro). Usada para enriquecer os dados de combustível com o tipo de vínculo do veículo.

---

### ORIGEM 5 — Razão de Veículo

| Atributo | Valor |
|---|---|
| **Componente** | `Origem RazaoVeiculo` |
| **Objeto** | `dbo.vwRazaoVeiculo` |
| **Tipo** | View |
| **Query** | `select * from [dbo].[vwRazaoVeiculo] with (nolock)` |
| **Destino** | `dbo.stg_RazaoVeiculo` |
| **Colunas** | 14 |

**Descrição**: View de razão contábil por veículo — movimentações de débito/crédito associadas a cada veículo (combustível, manutenção, pedágio, etc.).

---

## Colunas por Origem

### Origens 1 e 2 — vwrealabastecimento / vwrealabastecimento2 (27 colunas cada)

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|:---:|---|---|:---:|---|
| 1 | `Cdconta` | `DT_I4` (int) | — | Código da conta contábil |
| 2 | `CdVeiculo` | `DT_I4` (int) | — | Código do veículo |
| 3 | `NrPlaca` | `DT_STR` (varchar) | 8 | Placa do veículo |
| 4 | `CdEmpresa` | `DT_I4` (int) | — | Código da empresa/filial |
| 5 | `DSAPELIDO` | `DT_STR` (varchar) | 10 | Apelido / código da filial |
| 6 | `NrControle` | `DT_I4` (int) | — | Número de controle do abastecimento |
| 7 | `NrDocumentoExt` | `DT_I4` (int) | — | Número do documento externo |
| 8 | `CdInscricao` | `DT_STR` (varchar) | 14 | CNPJ/CPF do posto |
| 9 | `DsEntidade` | `DT_STR` (varchar) | 100 | Nome do posto / fornecedor |
| 10 | `NrCEP` | `DT_I4` (int) | — | CEP do posto |
| 11 | `DsBairro` | `DT_STR` (varchar) | 30 | Bairro do posto |
| 12 | `DsLocal` | `DT_STR` (varchar) | 30 | Cidade do posto |
| 13 | `DsUF` | `DT_STR` (varchar) | 2 | UF do posto |
| 14 | `DtLancamento` | `DT_DBTIMESTAMP` (datetime) | — | Data do abastecimento |
| 15 | `CdModelo` | `DT_I4` (int) | — | Código do modelo do veículo |
| 16 | `DsModelo` | `DT_STR` (varchar) | 40 | Descrição do modelo |
| 17 | `DsAnoModelo` | `DT_I4` (int) | — | Ano do modelo |
| 18 | `CdTipoVeiculo` | `DT_I4` (int) | — | Código do tipo de veículo |
| 19 | `DsTpVeiculo` | `DT_STR` (varchar) | 40 | Descrição do tipo de veículo |
| 20 | `DsCombustivel` | `DT_STR` (varchar) | 40 | Tipo de combustível |
| 21 | `VLTotal` | `DT_CY` (currency) | — | Valor total do abastecimento |
| 22 | `Quantidade` | `DT_CY` (currency) | — | Quantidade de litros abastecidos |
| 23 | `NrHodometroLanca` | `DT_I4` (int) | — | Hodômetro no momento do abastecimento |
| 24 | `NrHodUltRevisao` | `DT_I4` (int) | — | Hodômetro da última revisão |
| 25 | `QtMediaApurada` | `DT_CY` (currency) | — | Média de consumo apurada (km/L) |
| 26 | `KM` | `DT_I4` (int) | — | KM percorridos desde o último abastecimento |
| 27 | `TIPO` | `DT_STR` (varchar) | 8 | Tipo de vínculo do veículo (próprio/terceiro/agregado) |

> **Nota**: `VLTotal`, `Quantidade` e `QtMediaApurada` usam tipo `DT_CY` (Currency). No SQL Server, isso mapeia para `money`. Verifique se as colunas de destino são `money` ou `decimal` — diferenças de escala podem ocorrer.

---

### Origem 3 — Abasteciemtos (5 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|:---:|---|---|:---:|---|
| 1 | `nrplaca` | `DT_STR` (varchar) | 8 | Placa do veículo |
| 2 | `vlevento` | `DT_NUMERIC` | 14,4 | Valor do evento (desconto) |
| 3 | `Dtemissao` | `DT_STR` (varchar) | 30 | Data de emissão (armazenada como string!) |
| 4 | `cdempresa` | `DT_I4` (int) | — | Código da empresa |
| 5 | `cdcartafrete` | `DT_I4` (int) | — | Código da carta de frete |

---

### Origem 4 — TipoVeiculo (2 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|:---:|---|---|:---:|---|
| 1 | `nrplaca` | `DT_STR` (varchar) | 8 | Placa do veículo |
| 2 | `TIPO` | `DT_STR` (varchar) | 1 | Tipo: A (Agregado), P (Próprio), T (Terceiro) |

---

### Origem 5 — vwRazaoVeiculo (14 colunas)

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|:---:|---|---|:---:|---|
| 1 | `CdVeiculo` | `DT_I4` (int) | — | Código do veículo |
| 2 | `NrPlaca` | `DT_STR` (varchar) | 8 | Placa do veículo |
| 3 | `Tpveiculo` | `DT_I4` (int) | — | Código do tipo de veículo |
| 4 | `TipoDeVeiculo` | `DT_STR` (varchar) | 40 | Descrição do tipo de veículo |
| 5 | `DtLancamento` | `DT_STR` (varchar) | 30 | Data do lançamento (armazenada como string!) |
| 6 | `DsConta` | `DT_STR` (varchar) | 40 | Descrição da conta contábil |
| 7 | `ValorTotal` | `DT_CY` (currency) | — | Valor total do lançamento |
| 8 | `CreditoDebito` | `DT_STR` (varchar) | 1 | `C` (Crédito) ou `D` (Débito) |
| 9 | `NrControle` | `DT_I4` (int) | — | Número de controle |
| 10 | `CdConta` | `DT_I4` (int) | — | Código da conta contábil |
| 11 | `CdSequencia` | `DT_I4` (int) | — | Sequência do lançamento |
| 12 | `NrContaContabil` | `DT_I4` (int) | — | Número da conta contábil |
| 13 | `DsTpDocumento` | `DT_STR` (varchar) | 40 | Tipo do documento |
| 14 | `DsApelido` | `DT_STR` (varchar) | 10 | Apelido / filial |

> **Nota**: `DtLancamento` vem como `DT_STR(30)` nesta view — data como texto. Verificar formato (DD/MM/AAAA ou AAAA-MM-DD) antes de usar em filtros ou cálculos no DW.
