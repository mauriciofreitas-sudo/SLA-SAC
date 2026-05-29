# ETL_Justificativa_Entrega — Origem

## Atributos do Servidor de Origem

| Atributo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | softran_translute |
| Usuario | softran |
| Provider | SQLOLEDB.1 |
| ConnectRetryCount | 1 |
| ConnectRetryInterval | 5s |
| ConnectionManager | SOFTRA - TRANSLUTE |

## Source Component: Origem Just_Entrega

| Atributo | Valor |
|---|---|
| ComponentClassID | Microsoft.OLEDBSource |
| AccessMode | 2 (SQL Command) |
| CommandTimeout | 0 (sem timeout) |
| DefaultCodePage | 1252 |

### Query SQL

```sql
select * from [dbo].[Justificativa_Entrega] with (nolock)
```

### Colunas de Saida

| # | Coluna | Tipo SSIS | Tamanho | Descricao Semantica |
|---|---|---|---|---|
| 1 | IDDOCUMENTO | DT_STR | 42 | Identificador do documento de transporte |
| 2 | CdHistoricoEntrega | DT_I4 | — | Codigo da justificativa/historico de entrega |
| 3 | DsHistoricoEntrega | DT_STR | 50 | Descricao textual da justificativa de entrega |
| 4 | dsusuario | DT_STR | 10 | Login do usuario que registrou o evento |
| 5 | CdSequencia | DT_I4 | — | Numero de sequencia do registro |
| 6 | HrMovimento | DT_DBTIMESTAMP | — | Hora/timestamp do movimento |
| 7 | DtMovimento | DT_DBTIMESTAMP | — | Data do movimento de entrega |
| 8 | dtdigitacao | DT_DBTIMESTAMP | — | Data/hora de digitacao do registro |
| 9 | dsorigemmovto | DT_STR | 30 | Origem do movimento (sistema gerador) |

**Total: 9 colunas**

### Saida de Erro

O componente possui saida de erro (`Saida de Erro de Origem OLE DB`) com as mesmas 9 colunas mais ErrorCode (DT_I4) e ErrorColumn (DT_I4). A saida de erro nao esta conectada a nenhum destino.
