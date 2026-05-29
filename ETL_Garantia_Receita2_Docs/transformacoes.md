# ETL_Garantia_Receita2 — Transformacoes

## Visao Geral

O Data Flow `Load fCTE` e um passthrough direto. Toda a logica de negocio esta encapsulada na view `vw_GARANTIA_RECEITA_V2` no servidor SOFTRAN. A grande diferenca em relacao ao v1 e o uso de ADO.NET (SqlBulkCopy) em vez de OLE DB FastLoad, e a inclusao da coluna `CdTitulo`.

## Diagrama ASCII do Data Flow

```
+--------------------------------------------+
|            ADO NET Source                  |
|  vw_GARANTIA_RECEITA_V2                    |
|  (169.57.181.231 / SOFTRAN_TRANSLUTE)     |
|  ADO.NET SqlClient                         |
|  CommandTimeout: 600s                      |
+--------------------------------------------+
                    |
                    | ADO NET Source Output (48 colunas)
                    v
+--------------------------------------------+
|           ADO NET Destination              |
|  "dbo"."GARANTIA_RECEITA"                  |
|  (10.100.86.89 / DBStage)                  |
|  SqlBulkCopy (UseBulkInsertWhenPossible)   |
|  CommandTimeout: 600s                      |
|  errorRowDisposition: FailComponent        |
+--------------------------------------------+
```

## Pre-processamento no Control Flow

```sql
-- Executado antes do Data Flow (task "Truncate fGarantia_Receita"):
TRUNCATE TABLE "GARANTIA_RECEITA"
```

## Transformacoes no Data Flow

**Nenhuma transformacao aplicada.** Fluxo 100% passthrough:
- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Sort / Aggregate

## Mapeamento de Tipos: Comparacao Origem vs Destino

| Coluna | Tipo Origem (ADO.NET) | Tipo Destino (external) | Diferenca |
|---|---|---|---|
| Tipo de Documento | i4 | wstr(40) | TIPO DIFERENTE — i4 na origem, wstr(40) no destino |
| Total Prestação | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| Total Frete | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| Valor Total da Fatura | numeric p38,s4 | numeric p15,s2 | Precisao e escala diferentes |
| Valor Pago | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| Acréscimos | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| Deduções | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| Saldo | numeric p14,s4 | numeric p15,s2 | Escala diferente |
| CdTitulo | wstr(15) | wstr(15) | Correto |
| Origem UF | wstr(2) | wstr(2) | Correto (corrigido vs v1) |
| Destino UF | wstr(2) | wstr(2) | Correto (corrigido vs v1) |

> Importante: `AllowImplicitStringConversion=true` no ADO NET Source permite a conversao implicita de i4 (Tipo de Documento) para wstr(40) no destino.

## Caminho dos Dados

```
Path: Package\Contêiner da Sequência\Load fCTE.Paths[ADO NET Source Output]
  Start: ADO NET Source.Outputs[ADO NET Source Output]
  End:   ADO NET Destination.Inputs[ADO NET Destination Input]
```
