# Transformações — ETL_ControladoriaFrete

## Resumo

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   ORIGEM                                        DESTINO          │
│   dbo.VW_CONTROLADORIA_DE_FRETE  ──►  dbo.fControladoriaFrete   │
│                                                                  │
│              PASSTHROUGH DIRETO                                  │
│              Nenhuma transformação no Data Flow                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

O pipeline **não contém transformações** no Data Flow. Os dados são transferidos diretamente da view de origem para a tabela de destino:

- Sem `Derived Column`
- Sem `Data Conversion`
- Sem `Lookup`
- Sem `Conditional Split`
- Sem `Aggregate`
- Sem `Script Component`

---

## Componentes do Data Flow

```
┌─────────────────────┐         ┌─────────────────────────┐
│   ADO NET Source    │         │   ADO NET Destination   │
│                     │         │                         │
│ • 169.57.181.231    │─────────│ • 10.100.86.89          │
│ • SOFTRAN_TRANSLUTE │         │ • DWGrupolc             │
│ • VW_CONTROLADORIA  │         │ • fControladoriaFrete   │
│   _DE_FRETE (view)  │         │                         │
└─────────────────────┘         └─────────────────────────┘
```

---

## Pré-processamento (fora do Data Flow)

### Execute SQL Task — "Truncate fControladoriaFrete"

```sql
DELETE FROM dbo.fControladoriaFrete
```

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 (DWGrupolc) |
| Conexão | OLE DB (`sqldba1`) |
| Efeito | Remove **todos** os registros antes da recarga |

---

## Transformações realizadas na view de origem

A view `VW_CONTROLADORIA_DE_FRETE` é responsável por todas as transformações de negócio. O ETL apenas move os dados já calculados:

| Coluna calculada | Lógica na view |
|---|---|
| `VlMargem` | `VlReceita - VlCustoCTRB` — resultado financeiro da operação |
| `PctMargem` | `(VlMargem / NULLIF(VlReceita, 0)) * 100` — margem percentual |
| `VlCustoCTRB` | `VlFreteCTRB + VlPedagioCTRB + VlEncargos + VlAluguelCarreta` |
| `VlEncargos` | `VlINSS + VlSESTSENAT + VlIRRF` — total de encargos previdenciários |
| `AnoMes` | `YEAR(DtEmissao) * 100 + MONTH(DtEmissao)` |
| `VinculoMotorista` | Lookup em cadastro de motoristas |

### JOIN realizado pela view

```sql
-- Estrutura conceitual dos JOINs da VW_CONTROLADORIA_DE_FRETE:
SELECT
    cte.*,
    ctrb.VlFreteCTRB,
    ctrb.VlPedagioCTRB,
    ctrb.VlAdiantamentoCTRB,
    ctrb.VlSaldoCTRB,
    ctrb.VlINSS + ctrb.VlSESTSENAT + ctrb.VlIRRF AS VlEncargos,
    ctrb.VlAluguelCarreta,
    (cte.VlReceita - ctrb.VlCustoCTRB) AS VlMargem,
    ...
FROM vwCTE_Base cte
INNER JOIN Ficha_de_Viagem_Geral fv ON cte.NrDoctoFiscal = fv.NrDoctoFiscal
LEFT  JOIN VW_CVLD ctrb ON fv.NrFicha = ctrb.NrFicha
```

> Nota: Esta é uma representação conceitual. A lógica real da view pode diferir.

---

## Mapeamento de Tipos de Dados

| Tipo na Origem (ADO.NET) | Tipo SSIS | Tipo no Destino |
|---|---|---|
| `int` | `DT_I4` | `int` |
| `datetime` | `DT_DBTIMESTAMP` | `datetime` |
| `numeric(14,4)` | `DT_NUMERIC (14,4)` | `numeric(14,4)` |
| `numeric(5,2)` | `DT_NUMERIC (5,2)` | `numeric(5,2)` |
| `nvarchar(n)` | `DT_WSTR (n)` | `nvarchar(n)` |
