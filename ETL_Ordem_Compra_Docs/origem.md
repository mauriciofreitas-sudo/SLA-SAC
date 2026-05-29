# ETL_Ordem_Compra — Origem

## Atributos do Servidor de Origem

| Atributo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | SOFTRAN_TRANSLUTE |
| Usuario | softran |
| Provider | ADO.NET (System.Data.SqlClient) |
| ConnectionManager | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc |

## Source Component: ADO NET Source

| Atributo | Valor |
|---|---|
| ComponentClassID | Microsoft.ManagedComponentHost (DataReaderSourceAdapter) |
| AccessMode | 0 (Table or View) |
| TableOrViewName | "dbo"."vw_Ordens_Compras" |
| CommandTimeout | 600 segundos |
| AllowImplicitStringConversion | true |
| validateExternalMetadata | false |

### Query SQL

```sql
SELECT * FROM "dbo"."vw_Ordens_Compras"
```

(A view encapsula a logica de negocio das ordens de compra)

### Colunas de Saida

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Descricao Semantica |
|---|---|---|---|---|
| 1 | EMISSAO | DT_DBTIMESTAMP | — | Data de emissao da ordem de compra |
| 2 | FILIAL | DT_WSTR | 10 | Codigo da filial emissora |
| 3 | OC | DT_I4 | — | Numero da ordem de compra |
| 4 | ITEM | DT_I4 | — | Numero do item na OC |
| 5 | DESCRICAO | DT_WSTR | 80 | Descricao do item |
| 6 | QTDE | DT_NUMERIC | 14,4 | Quantidade solicitada |
| 7 | VL_UNIT | DT_CY | — | Valor unitario |
| 8 | VL_TOTAL | DT_CY | — | Valor total do item |
| 9 | VlTotalRecebimento | DT_CY | — | Valor total recebido |
| 10 | DESC_NEGOC | DT_NUMERIC | 14,4 | Desconto negociado |
| 11 | ORCAMENTO | DT_I4 | — | Numero do orcamento vinculado |
| 12 | COND_PGTO | DT_WSTR | 40 | Condicao de pagamento |
| 13 | FORNECDOR | DT_WSTR | 100 | Nome do fornecedor (typo: FORNECDOR em vez de FORNECEDOR) |
| 14 | FRETE | DT_WSTR | 7 | Tipo/modalidade de frete |
| 15 | AUTORIZ_EM | DT_DBTIMESTAMP | — | Data de autorizacao da OC |
| 16 | Usuario_Compra | DT_WSTR | 100 | Usuario que realizou a compra |
| 17 | AUTORIZ_POR | DT_WSTR | 10 | Login do autorizador |
| 18 | UsuarioBaixa | DT_WSTR | 50 | Usuario que deu baixa na OC |
| 19 | BAIXA | DT_DBTIMESTAMP | — | Data da baixa/recebimento |
| 20 | NrContabilFiscal | DT_I4 | — | Numero contabil/fiscal associado |
| 21 | DsPlanoConta | DT_WSTR | 40 | Descricao do plano de contas |
| 22 | CdTipoItem | DT_I4 | — | Codigo do tipo de item |
| 23 | DsTipoItem | DT_WSTR | 30 | Descricao do tipo de item |

**Total: 23 colunas**

### Saida de Erro

O componente ADO NET Source possui saida de erro (`ADO NET Source Error Output`) com as mesmas 23 colunas mais ErrorCode e ErrorColumn. A saida de erro nao esta conectada.
