# Mapeamento de Colunas — ETL_BaseCTE

## Visão Geral

```
ORIGEM                              DESTINO
169.57.181.231                      10.100.86.89
SOFTRAN_TRANSLUTE                   DWGrupolc
dbo.vwCTE_Base (view)               dbo.fBaseCTE (tabela fato)

Todas as colunas: passthrough direto (sem renomeação / sem conversão)
```

---

## Mapeamento Completo

| # | Coluna (Origem = Destino) | Tipo SSIS | Tamanho | Grupo |
|:---:|---|---|:---:|---|
| 1 | `EmpresaCTE` | `DT_I4` | — | Identificação CT-e |
| 2 | `NrDoctoFiscal` | `DT_I4` | — | Identificação CT-e |
| 3 | `TipoDoctoFiscal` | `DT_WSTR` | 10 | Identificação CT-e |
| 4 | `DtEmissao` | `DT_DBTIMESTAMP` | — | Identificação CT-e |
| 5 | `AnoMes` | `DT_I4` | — | Identificação CT-e |
| 6 | `Ano` | `DT_I4` | — | Identificação CT-e |
| 7 | `Mes` | `DT_I4` | — | Identificação CT-e |
| 8 | `Filial` | `DT_WSTR` | 10 | Filial / Localização |
| 9 | `DsFilial` | `DT_WSTR` | 100 | Filial / Localização |
| 10 | `UFOrigem` | `DT_WSTR` | 2 | Filial / Localização |
| 11 | `CidadeOrigem` | `DT_WSTR` | 50 | Filial / Localização |
| 12 | `UFDestino` | `DT_WSTR` | 2 | Filial / Localização |
| 13 | `CidadeDestino` | `DT_WSTR` | 50 | Filial / Localização |
| 14 | `CdRemetente` | `DT_WSTR` | 14 | Partes Envolvidas |
| 15 | `DsRemetente` | `DT_WSTR` | 100 | Partes Envolvidas |
| 16 | `CdDestinatario` | `DT_WSTR` | 14 | Partes Envolvidas |
| 17 | `DsDestinatario` | `DT_WSTR` | 100 | Partes Envolvidas |
| 18 | `CdPagador` | `DT_WSTR` | 14 | Partes Envolvidas |
| 19 | `DsPagador` | `DT_WSTR` | 100 | Partes Envolvidas |
| 20 | `CdGrupoCliente` | `DT_I4` | — | Partes Envolvidas |
| 21 | `DsGrupoCliente` | `DT_WSTR` | 100 | Partes Envolvidas |
| 22 | `CdMotorista` | `DT_WSTR` | 14 | Motorista / Veículo |
| 23 | `DsMotorista` | `DT_WSTR` | 100 | Motorista / Veículo |
| 24 | `VinculoMotorista` | `DT_WSTR` | 20 | Motorista / Veículo |
| 25 | `Veiculo` | `DT_WSTR` | 8 | Motorista / Veículo |
| 26 | `Carreta` | `DT_WSTR` | 8 | Motorista / Veículo |
| 27 | `VlReceita` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 28 | `VlFrete` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 29 | `VlMargem` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 30 | `PctMargem` | `DT_NUMERIC` | 5,2 | Valores Financeiros |
| 31 | `VlFretePeso` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 32 | `VlFreteValor` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 33 | `VlPedagio` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 34 | `VlGRIS` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 35 | `VlTotalPrestacao` | `DT_NUMERIC` | 14,4 | Valores Financeiros |
| 36 | `NrFicha` | `DT_I4` | — | Ficha / CTRB |
| 37 | `NrCTRB` | `DT_I4` | — | Ficha / CTRB |
| 38 | `DtEmissaoCTRB` | `DT_DBTIMESTAMP` | — | Ficha / CTRB |

---

## Distribuição por Grupo

```
Identificação CT-e    ███████         7 colunas
Filial / Localização  ██████          6 colunas
Partes Envolvidas     ████████        8 colunas
Motorista / Veículo   █████           5 colunas
Valores Financeiros   █████████       9 colunas
Ficha / CTRB          ███             3 colunas
                      ──────────────────────────
TOTAL                 38 colunas
```

---

## Tipos de Dados

| Tipo SSIS | Equivalente SQL | Quantidade |
|---|---|:---:|
| `DT_I4` | `int` | 9 |
| `DT_DBTIMESTAMP` | `datetime` | 3 |
| `DT_NUMERIC(14,4)` | `numeric(14,4)` | 8 |
| `DT_NUMERIC(5,2)` | `numeric(5,2)` | 1 |
| `DT_WSTR` | `nvarchar` | 17 |
