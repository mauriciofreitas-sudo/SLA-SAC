# Transformações — ETL_CVLD

## Resumo

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   ORIGEM                                       DESTINO           │
│   dbo.VW_CVLD          ────────────►   dbo.fCVLD                 │
│                                                                  │
│              PASSTHROUGH DIRETO                                  │
│              Nenhuma transformação no Data Flow                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

O pipeline **não contém transformações** no Data Flow. Todos os campos são transferidos diretamente da view para a tabela destino:

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
│ • VW_CVLD (view)    │         │ • fCVLD                 │
└─────────────────────┘         └─────────────────────────┘
```

---

## Pré-processamento (fora do Data Flow)

### Execute SQL Task — "Truncate fCVLD"

```sql
DELETE FROM dbo.fCVLD
```

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 (DWGrupolc) |
| Conexão | OLE DB (`sqldba1`) |
| Efeito | Remove **todos** os registros antes da recarga |

---

## Cálculos realizados na view de origem

Embora o ETL não transforme dados, a view `VW_CVLD` entrega colunas pré-calculadas:

| Coluna calculada | Lógica provável na view |
|---|---|
| `VlSaldoCTRB` | `VlFreteCTRB - VlAdiantamentoCTRB` — saldo a pagar |
| `AnoMes` | `YEAR(DtEmissaoCTRB) * 100 + MONTH(DtEmissaoCTRB)` |
| `TipoVinculo` | Lookup em cadastro de motoristas → `'Terceiro'` ou `'Funcionario'` |
| `FlAluguel` | `CASE WHEN VlAluguelCarreta > 0 THEN 1 ELSE 0 END` |

---

## Mapeamento de Tipos de Dados

| Tipo na Origem (ADO.NET) | Tipo SSIS | Tipo no Destino |
|---|---|---|
| `int` | `DT_I4` | `int` |
| `datetime` | `DT_DBTIMESTAMP` | `datetime` |
| `numeric(14,4)` | `DT_NUMERIC (14,4)` | `numeric(14,4)` |
| `nvarchar(n)` | `DT_WSTR (n)` | `nvarchar(n)` |
| `bit` | `DT_BOOL` | `bit` |
