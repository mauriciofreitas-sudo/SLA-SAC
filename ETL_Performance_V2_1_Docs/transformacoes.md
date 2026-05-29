# ETL_Performance_V2 (1) — Transformações

## Visão Geral

O Data Flow é **idêntico** ao `ETL_Performance_V2.dtsx` — passthrough completo de 43 colunas sem componentes de transformação intermediários.

A diferença desta versão está exclusivamente na task de pré-processamento ("Truncate fPerf").

## Diagrama ASCII — Data Flow (idêntico ao V2)

```
[ADO NET Source]
  |-- Origem: dbo.vw_fPerformance (169.57.181.231)
  |-- Modo: TableOrViewName (sem SQL inline)
  |-- CommandTimeout: 30s
  |-- 43 colunas passadas diretamente
        |
        | (passthrough — sem transformações SSIS)
        v
[ADO NET Destination]
  |-- Destino: dbo.fPerformance (10.100.86.89 / DBStage)
  |-- SqlBulkCopy habilitado
  |-- BatchSize: 50.000
  |-- CommandTimeout: 30s
```

## Lógica da Task "Truncate fPerf" — VERSÃO ATUALIZADA

Esta versão implementa um DELETE incremental com loop para evitar locks longos:

```sql
WHILE 1 = 1
BEGIN
    DELETE
    FROM dbo.fPerformance
    WHERE NFDTEmissao >= DATEADD(MONTH, -1, GETDATE());

    IF @@ROWCOUNT = 0
        BREAK;
END
```

### Comportamento esperado:
1. Deleta registros do último mês em lotes
2. Continua até `@@ROWCOUNT = 0` (não há mais registros a deletar)
3. Carrega os dados atuais da view `vw_fPerformance` para preencher o período

### Análise do filtro:
- `DATEADD(MONTH, -1, GETDATE())` — data de 1 mês atrás a partir da execução
- O pacote então recarrega todos os registros da view (sem filtro), incluindo possivelmente dados mais antigos que 1 mês

**Inconsistência identificada:** Se a view retornar dados além do último mês, haverá sobreposição de carga sem DELETE para o período mais antigo.

## Tipos de Dados — Consistência

Idêntico ao `ETL_Performance_V2.dtsx`:

| Grupo           | Quantidade |
|-----------------|------------|
| wstr            | 21         |
| i4 (int)        | 13         |
| dbTimeStamp     | 9          |
| numeric p=14s4  | 3          |
| **Total**       | **43**     |

Todos os campos de data usam `dbTimeStamp` (datetime nativo) — diferente do ETL_Painel_CTE que usa strings.
