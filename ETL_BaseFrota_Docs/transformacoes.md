# ETL_BaseFrota — Transformacoes

## Visao Geral

O Data Flow `Load fBaseFrota` e um passthrough direto: os dados saem da origem (ADO NET Source) e vao direto para o destino (ADO NET Destination) sem nenhuma transformacao intermediaria.

```
[ADO NET Source]  --(ADO NET Source Output)-->  [ADO NET Destination]
  vw_ConsultaVeiculos                              fBaseFrota
```

## Diagrama ASCII do Data Flow

```
+----------------------------------+
|         ADO NET Source           |
|  vw_ConsultaVeiculos             |
|  (169.57.181.231/SOFTRAN_TRANS.) |
+----------------------------------+
              |
              | ADO NET Source Output (20 colunas)
              v
+----------------------------------+
|       ADO NET Destination        |
|  fBaseFrota                      |
|  (10.100.86.89/DWGrupolc)        |
+----------------------------------+
```

## Pre-processamento no Control Flow

```sql
-- Executado pela task "Truncate fBaseFrota" antes do Data Flow:
TRUNCATE TABLE "fBaseFrota"
```

## Transformacoes no Data Flow

**Nenhuma transformacao aplicada.** O fluxo e 100% passthrough:

- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Sort
- Sem Aggregate
- Sem Conditional Split
- Sem Merge/Union All

## Mapeamento de Tipos de Dados

| Coluna | Tipo na Origem (SSIS) | Tipo no Destino (SSIS) | Conversao Implicita |
|---|---|---|---|
| NrPlaca | wstr(8) | wstr(8) | Nenhuma |
| CdFabricRastreador | i4 | i4 | Nenhuma |
| DsFabricante | wstr(40) | wstr(40) | Nenhuma |
| NumeroRastreador | i4 | i4 | Nenhuma |
| NrAnoFabricacao | i4 | i4 | Nenhuma |
| DsCores | wstr(40) | wstr(40) | Nenhuma |
| NrChassis | wstr(25) | wstr(25) | Nenhuma |
| DtCadastro | dbTimeStamp | dbTimeStamp | Nenhuma |
| DsUsuarioInc | wstr(10) | wstr(10) | Nenhuma |
| DtAtualizacao | dbTimeStamp | dbTimeStamp | Nenhuma |
| DsUsuAlteracao | wstr(10) | wstr(10) | Nenhuma |
| NrRenavan | wstr(15) | wstr(15) | Nenhuma |
| DsObservacao | wstr(500) | wstr(500) | Nenhuma |
| Tipo | wstr(8) | wstr(8) | Nenhuma |
| Proprietario | wstr(60) | wstr(60) | Nenhuma |
| DsTpVeiculo | wstr(40) | wstr(40) | Nenhuma |
| CdEmpresa | i4 | i4 | Nenhuma |
| Situacao | wstr(7) | wstr(7) | Nenhuma |
| VeicAnterior | wstr(8) | wstr(8) | Nenhuma |
| CdANTT | wstr(20) | wstr(20) | Nenhuma |

**Nota:** O destino usa ADO.NET com `AllowImplicitStringConversion=true` na origem, o que permite conversoes implicitas de tipos nao-string para string se necessario.

## Caminho dos Dados

```
Path ID: Package\Load fBaseFrota.Paths[ADO NET Source Output]
  Start:  Package\Load fBaseFrota\ADO NET Source.Outputs[ADO NET Source Output]
  End:    Package\Load fBaseFrota\ADO NET Destination.Inputs[ADO NET Destination Input]
```
