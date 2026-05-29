# ETL_Justificativa_Entrega — Transformacoes

## Resumo

Este package e um **passthrough puro**: nenhuma transformacao e aplicada entre a origem e o destino. Os dados fluem diretamente da tabela `Justificativa_Entrega` no Softran para `stg_Justificativa_Entrega` no DBStage.

## Diagrama ASCII do Data Flow

```
[Origem Just_Entrega]
  SELECT * FROM [dbo].[Justificativa_Entrega] WITH (NOLOCK)
  (169.57.181.231 / softran_translute)
         |
         | 9 colunas passthrough
         |
[Justificativa_Entrega (Destino)]
  INSERT → [dbo].[stg_Justificativa_Entrega]
  (10.100.86.89 / DBStage)
```

## Pre-Processamento SQL

Antes do Data Flow, a `Tarefa Executar SQL` executa:

```sql
delete from [dbo].[stg_Justificativa_Entrega]
```

Nao ha parametros, variaveis ou logica condicional.

## Componentes de Transformacao

Nenhum componente de transformacao intermediario (sem Derived Column, Lookup, Data Conversion, Conditional Split, Aggregate, Sort, Merge, etc.).

## Mapeamento de Tipos

| Coluna | Tipo Origem (Softran) | Tipo SSIS | Tipo Destino (DBStage) | Observacao |
|---|---|---|---|---|
| IDDOCUMENTO | varchar(42) | DT_STR(42) | varchar(70) | Destino e maior — sem risco de truncamento |
| CdHistoricoEntrega | int | DT_I4 | int | Compativel |
| DsHistoricoEntrega | varchar(50) | DT_STR(50) | varchar(50) | Compativel |
| dsusuario | varchar(10) | DT_STR(10) | varchar(10) | Compativel |
| CdSequencia | int | DT_I4 | int | Compativel |
| HrMovimento | datetime | DT_DBTIMESTAMP | datetime | Compativel |
| DtMovimento | datetime | DT_DBTIMESTAMP | datetime | Compativel |
| dtdigitacao | datetime | DT_DBTIMESTAMP | datetime | Compativel |
| dsorigemmovto | varchar(30) | DT_STR(30) | varchar(30) | Compativel |

## Codepage

Todos os campos string usam codepage 1252 (Windows Latin 1 / ANSI). Nao ha conversao de Unicode.
