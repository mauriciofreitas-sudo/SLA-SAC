# Mapeamento de Colunas — ETL_CVLD

## Visão Geral

```
ORIGEM                              DESTINO
169.57.181.231                      10.100.86.89
SOFTRAN_TRANSLUTE                   DWGrupolc
dbo.VW_CVLD (view)                  dbo.fCVLD (tabela fato)

Passthrough direto — sem renomeação / sem conversão de tipo
```

---

## Mapeamento Completo

| # | Coluna (Origem = Destino) | Tipo SSIS | Tamanho | Grupo |
|:---:|---|---|:---:|---|
| 1 | `NumeroCTRB` | `DT_I4` | — | Identificação CTRB |
| 2 | `EmpresaCTRB` | `DT_I4` | — | Identificação CTRB |
| 3 | `DtEmissaoCTRB` | `DT_DBTIMESTAMP` | — | Identificação CTRB |
| 4 | `DtPagamentoCTRB` | `DT_DBTIMESTAMP` | — | Identificação CTRB |
| 5 | `StatusCTRB` | `DT_WSTR` | 20 | Identificação CTRB |
| 6 | `AnoMes` | `DT_I4` | — | Identificação CTRB |
| 7 | `Filial` | `DT_WSTR` | 10 | Filial / Localização |
| 8 | `UFOrigem` | `DT_WSTR` | 2 | Filial / Localização |
| 9 | `UFDestino` | `DT_WSTR` | 2 | Filial / Localização |
| 10 | `CdTransportadora` | `DT_WSTR` | 14 | Transportadora |
| 11 | `DsTransportadora` | `DT_WSTR` | 100 | Transportadora |
| 12 | `CdMotorista` | `DT_WSTR` | 14 | Transportadora |
| 13 | `DsMotorista` | `DT_WSTR` | 100 | Transportadora |
| 14 | `TipoVinculo` | `DT_WSTR` | 20 | Transportadora |
| 15 | `Veiculo` | `DT_WSTR` | 8 | Transportadora |
| 16 | `Carreta` | `DT_WSTR` | 8 | Transportadora |
| 17 | `NrFicha` | `DT_I4` | — | Ficha de Viagem |
| 18 | `DtEmissaoFicha` | `DT_DBTIMESTAMP` | — | Ficha de Viagem |
| 19 | `VlFreteCTRB` | `DT_NUMERIC` | 14,4 | Valores CTRB |
| 20 | `VlSaldoCTRB` | `DT_NUMERIC` | 14,4 | Valores CTRB |
| 21 | `VlAdiantamentoCTRB` | `DT_NUMERIC` | 14,4 | Valores CTRB |
| 22 | `VlPedagioCTRB` | `DT_NUMERIC` | 14,4 | Valores CTRB |
| 23 | `VlINSS` | `DT_NUMERIC` | 14,4 | Encargos / Aluguel |
| 24 | `VlSESTSENAT` | `DT_NUMERIC` | 14,4 | Encargos / Aluguel |
| 25 | `VlIRRF` | `DT_NUMERIC` | 14,4 | Encargos / Aluguel |
| 26 | `VlAluguelCarreta` | `DT_NUMERIC` | 14,4 | Encargos / Aluguel |
| 27 | `FlAluguel` | `DT_BOOL` | — | Encargos / Aluguel |

---

## Distribuição por Grupo

```
Identificação CTRB    ██████          6 colunas
Filial / Localização  ███             3 colunas
Transportadora        ███████         7 colunas
Ficha de Viagem       ██              2 colunas
Valores CTRB          ████            4 colunas
Encargos / Aluguel    █████           5 colunas
                      ──────────────────────────
TOTAL                 27 colunas
```

---

## Tipos de Dados

| Tipo SSIS | Equivalente SQL | Quantidade |
|---|---|:---:|
| `DT_I4` | `int` | 6 |
| `DT_DBTIMESTAMP` | `datetime` | 4 |
| `DT_NUMERIC(14,4)` | `numeric(14,4)` | 8 |
| `DT_WSTR` | `nvarchar` | 8 |
| `DT_BOOL` | `bit` | 1 |

---

## Referência — Composição de Custo no Dashboard

As colunas de encargos e aluguel são agregadas no dashboard para compor o gráfico de "Composição do Custo CTRB":

| Coluna | Valor Jan–Mai/2026 | % do Total |
|---|---|---|
| `VlFreteCTRB` | R$ 136.429.967 | 90,9% |
| `VlPedagioCTRB` | R$ 11.222.280 | 7,5% |
| `VlINSS` | R$ 1.711.678 | 1,1% |
| `VlSESTSENAT` | R$ 406.389 | 0,3% |
| `VlAluguelCarreta` | R$ 151.250 | 0,1% |
| `VlIRRF` | R$ 107.521 | 0,1% |
| **Total** | **R$ 150.029.085** | **100%** |
