# Mapeamento de Colunas — ETL_ControladoriaFrete

## Visão Geral

```
ORIGEM                              DESTINO
169.57.181.231                      10.100.86.89
SOFTRAN_TRANSLUTE                   DWGrupolc
dbo.VW_CONTROLADORIA_DE_FRETE       dbo.fControladoriaFrete
(view — cruza CT-e + Ficha + CTRB)  (tabela fato)

Passthrough direto — sem renomeação / sem conversão de tipo
```

---

## Mapeamento Completo

| # | Coluna (Origem = Destino) | Tipo SSIS | Tamanho | Grupo |
|:---:|---|---|:---:|---|
| 1 | `EmpresaCTE` | `DT_I4` | — | Identificação |
| 2 | `NrDoctoFiscal` | `DT_I4` | — | Identificação |
| 3 | `TipoDoctoFiscal` | `DT_WSTR` | 10 | Identificação |
| 4 | `DtEmissao` | `DT_DBTIMESTAMP` | — | Identificação |
| 5 | `AnoMes` | `DT_I4` | — | Identificação |
| 6 | `NrFicha` | `DT_I4` | — | Identificação |
| 7 | `NrCTRB` | `DT_I4` | — | Identificação |
| 8 | `Filial` | `DT_WSTR` | 10 | Filial / Rota |
| 9 | `UFOrigem` | `DT_WSTR` | 2 | Filial / Rota |
| 10 | `CidadeOrigem` | `DT_WSTR` | 50 | Filial / Rota |
| 11 | `UFDestino` | `DT_WSTR` | 2 | Filial / Rota |
| 12 | `CidadeDestino` | `DT_WSTR` | 50 | Filial / Rota |
| 13 | `CdRota` | `DT_I4` | — | Filial / Rota |
| 14 | `CdRemetente` | `DT_WSTR` | 14 | Cliente |
| 15 | `DsRemetente` | `DT_WSTR` | 100 | Cliente |
| 16 | `CdDestinatario` | `DT_WSTR` | 14 | Cliente |
| 17 | `DsDestinatario` | `DT_WSTR` | 100 | Cliente |
| 18 | `CdPagador` | `DT_WSTR` | 14 | Cliente |
| 19 | `DsPagador` | `DT_WSTR` | 100 | Cliente |
| 20 | `CdGrupoCliente` | `DT_I4` | — | Cliente |
| 21 | `DsGrupoCliente` | `DT_WSTR` | 100 | Cliente |
| 22 | `CdMotorista` | `DT_WSTR` | 14 | Motorista |
| 23 | `DsMotorista` | `DT_WSTR` | 100 | Motorista |
| 24 | `CdTransportadora` | `DT_WSTR` | 14 | Motorista |
| 25 | `DsTransportadora` | `DT_WSTR` | 100 | Motorista |
| 26 | `VinculoMotorista` | `DT_WSTR` | 20 | Motorista |
| 27 | `Veiculo` | `DT_WSTR` | 8 | Motorista |
| 28 | `Carreta` | `DT_WSTR` | 8 | Motorista |
| 29 | `VlReceita` | `DT_NUMERIC` | 14,4 | Receita |
| 30 | `VlFretePeso` | `DT_NUMERIC` | 14,4 | Receita |
| 31 | `VlFreteValor` | `DT_NUMERIC` | 14,4 | Receita |
| 32 | `VlPedagioReceita` | `DT_NUMERIC` | 14,4 | Receita |
| 33 | `VlGRIS` | `DT_NUMERIC` | 14,4 | Receita |
| 34 | `VlCustoCTRB` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 35 | `VlFreteCTRB` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 36 | `VlPedagioCTRB` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 37 | `VlAdiantamentoCTRB` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 38 | `VlSaldoCTRB` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 39 | `VlEncargos` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 40 | `VlAluguelCarreta` | `DT_NUMERIC` | 14,4 | Custo CTRB |
| 41 | `VlMargem` | `DT_NUMERIC` | 14,4 | Resultado |
| 42 | `PctMargem` | `DT_NUMERIC` | 5,2 | Resultado |

---

## Distribuição por Grupo

```
Identificação         ███████          7 colunas
Filial / Rota         ██████           6 colunas
Cliente               ████████         8 colunas
Motorista / Transp.   ███████          7 colunas
Receita (CT-e)        █████            5 colunas
Custo (CTRB)          ███████          7 colunas
Resultado (Margem)    ██               2 colunas
                      ──────────────────────────
TOTAL                 42 colunas
```

---

## Tipos de Dados

| Tipo SSIS | Equivalente SQL | Quantidade |
|---|---|:---:|
| `DT_I4` | `int` | 9 |
| `DT_DBTIMESTAMP` | `datetime` | 2 |
| `DT_NUMERIC(14,4)` | `numeric(14,4)` | 12 |
| `DT_NUMERIC(5,2)` | `numeric(5,2)` | 1 |
| `DT_WSTR` | `nvarchar` | 18 |

---

## Relação entre Colunas de Receita e Custo

```
VlReceita  (receita do CT-e)
    │
    ├── VlFretePeso
    ├── VlFreteValor
    ├── VlPedagioReceita
    └── VlGRIS

VlCustoCTRB  (custo do CTRB)
    │
    ├── VlFreteCTRB
    ├── VlPedagioCTRB
    ├── VlAdiantamentoCTRB  (já pago — componente do VlFreteCTRB)
    ├── VlSaldoCTRB         (a pagar — VlFreteCTRB - VlAdiantamentoCTRB)
    ├── VlEncargos          (INSS + SEST/SENAT + IRRF)
    └── VlAluguelCarreta

VlMargem = VlReceita - VlCustoCTRB
PctMargem = VlMargem / VlReceita × 100
```
