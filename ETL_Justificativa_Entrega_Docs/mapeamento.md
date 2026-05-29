# ETL_Justificativa_Entrega — Mapeamento Completo

## Visao Geral

| Aspecto | Valor |
|---|---|
| Servidor Origem | 169.57.181.231 (softran_translute) |
| Servidor Destino | 10.100.86.89 (DBStage) |
| Total de Colunas | 9 |
| Fluxos de Dados | 1 |

## Mapeamento Completo

| # | Coluna | Tipo SSIS | Tamanho | Grupo |
|---|---|---|---|---|
| 1 | IDDOCUMENTO | DT_STR | 42 | Identificacao |
| 2 | CdHistoricoEntrega | DT_I4 | — | Codigo |
| 3 | DsHistoricoEntrega | DT_STR | 50 | Descricao |
| 4 | dsusuario | DT_STR | 10 | Auditoria |
| 5 | CdSequencia | DT_I4 | — | Controle |
| 6 | HrMovimento | DT_DBTIMESTAMP | — | Data/Hora |
| 7 | DtMovimento | DT_DBTIMESTAMP | — | Data/Hora |
| 8 | dtdigitacao | DT_DBTIMESTAMP | — | Data/Hora |
| 9 | dsorigemmovto | DT_STR | 30 | Descricao |

## Distribuicao por Grupo

```
Identificacao  [====================] 1 coluna  (11%)
Codigo         [====================] 1 coluna  (11%)
Descricao      [========================================] 2 colunas (22%)
Auditoria      [====================] 1 coluna  (11%)
Controle       [====================] 1 coluna  (11%)
Data/Hora      [============================================================] 3 colunas (33%)
```

## Totais por Tipo de Dado

| Tipo SSIS | Quantidade | Percentual |
|---|---|---|
| DT_STR (varchar/ANSI) | 4 | 44% |
| DT_I4 (integer) | 2 | 22% |
| DT_DBTIMESTAMP (datetime) | 3 | 33% |
| **Total** | **9** | **100%** |
