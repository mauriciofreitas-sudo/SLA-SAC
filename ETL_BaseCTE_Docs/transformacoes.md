# Transformações — ETL_BaseCTE

## Resumo

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   ORIGEM                                        DESTINO          │
│   dbo.vwCTE_Base          ────────────►   dbo.fBaseCTE           │
│                                                                  │
│              PASSTHROUGH DIRETO                                  │
│              Nenhuma transformação no Data Flow                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

O pipeline **não contém transformações intermediárias** no Data Flow. Os dados são transferidos diretamente da view de origem para a tabela de destino:

- Sem `Derived Column`
- Sem `Data Conversion`
- Sem `Lookup`
- Sem `Conditional Split`
- Sem `Aggregate`
- Sem `Sort`
- Sem `Script Component`

---

## Componentes do Data Flow

```
┌─────────────────────┐         ┌─────────────────────────┐
│   ADO NET Source    │         │   ADO NET Destination   │
│                     │         │                         │
│ • 169.57.181.231    │─────────│ • 10.100.86.89          │
│ • SOFTRAN_TRANSLUTE │         │ • DWGrupolc             │
│ • vwCTE_Base (view) │         │ • fBaseCTE              │
└─────────────────────┘         └─────────────────────────┘
        Source                          Destination
   (sem transformação)
```

---

## Pré-processamento (fora do Data Flow)

### Execute SQL Task — "Truncate fBaseCTE"

```sql
DELETE FROM dbo.fBaseCTE
```

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 (DWGrupolc) |
| Conexão | OLE DB (`sqldba1`) |
| Efeito | Remove **todos** os registros antes da recarga |
| Estratégia | Full-refresh |

---

## Transformações realizadas na view de origem

Embora o ETL em si não aplique transformações, a view `dbo.vwCTE_Base` já entrega dados com colunas calculadas. As principais transformações ocorrem **dentro da view** no servidor de origem:

| Coluna calculada | Lógica provável na view |
|---|---|
| `VlMargem` | `VlReceita - VlFrete` |
| `PctMargem` | `(VlMargem / VlReceita) * 100` |
| `AnoMes` | `YEAR(DtEmissao) * 100 + MONTH(DtEmissao)` |
| `Ano` | `YEAR(DtEmissao)` |
| `Mes` | `MONTH(DtEmissao)` |
| `VinculoMotorista` | Lookup em tabela de motoristas → `'Terceiro'` ou `'Funcionario'` |

---

## Mapeamento de Tipos de Dados

| Tipo na Origem (ADO.NET) | Tipo SSIS | Tipo esperado no Destino |
|---|---|---|
| `int` | `DT_I4` | `int` |
| `datetime` | `DT_DBTIMESTAMP` | `datetime` |
| `numeric(14,4)` | `DT_NUMERIC (14,4)` | `numeric(14,4)` |
| `numeric(5,2)` | `DT_NUMERIC (5,2)` | `numeric(5,2)` |
| `nvarchar(n)` | `DT_WSTR (n)` | `nvarchar(n)` |
