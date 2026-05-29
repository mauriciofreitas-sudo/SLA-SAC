# Destino dos Dados — ETL_Base_Controladoria

## Identificação do Servidor de Destino

| Propriedade | Valor |
|-------------|-------|
| Servidor | 10.100.86.89 |
| Banco de Dados | DBStage |
| Usuario | sqldba |
| Tipo de Conexao | ADO.NET — System.Data.SqlClient |
| Connection Manager | 10.100.86.89 |
| Encrypt | False |
| TrustServerCertificate | True |

---

## ADO NET Destination — `VW_CONTROLADORIA_BASE_EMISSAO`

| Propriedade | Valor |
|-------------|-------|
| Tabela/View destino | `"dbo"."VW_CONTROLADORIA_BASE_EMISSAO"` |
| BatchSize | 0 (buffer SSIS padrao) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true |
| Estrategia de carga | **Full Replace** — DELETE em `fControladoria` antes do INSERT via view |
| errorRowDisposition (input) | **IgnoreFailure** |

> Importante: O destino e uma VIEW (`VW_CONTROLADORIA_BASE_EMISSAO`) e nao diretamente a tabela `fControladoria`. O package insere via view — isso requer que a view seja INSTEAD OF insertable ou que seja simplesmente um alias da tabela.

### Colunas Mapeadas — `VW_CONTROLADORIA_BASE_EMISSAO` (17 colunas)

| # | Coluna | Tipo SSIS Destino | Tamanho/Precisao | Origem |
|---|--------|-------------------|-----------------|--------|
| 1 | ID | wstr | 1 | VW_CONTROLADORIA_DE_FRETE.ID |
| 2 | DsEmpresa | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DsEmpresa |
| 3 | NrFicha | wstr | 15 | VW_CONTROLADORIA_DE_FRETE.NrFicha |
| 4 | DtEmissao | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DtEmissao |
| 5 | DtSaida | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DtSaida |
| 6 | DsCidadeOrigem | wstr | 33 | VW_CONTROLADORIA_DE_FRETE.DsCidadeOrigem |
| 7 | DsDestino | wstr | 33 | VW_CONTROLADORIA_DE_FRETE.DsDestino |
| 8 | DsCliente | wstr | 100 | VW_CONTROLADORIA_DE_FRETE.DsCliente |
| 9 | DsTipoTransporte | wstr | 40 | VW_CONTROLADORIA_DE_FRETE.DsTipoTransporte |
| 10 | VlMercadoria | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.VlMercadoria |
| 11 | NrCTRB | i4 | — | VW_CONTROLADORIA_DE_FRETE.NrCTRB |
| 12 | VlFreteCTRB | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlFreteCTRB |
| 13 | VlAdiantamento | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlAdiantamento |
| 14 | VlPedagioCTRB | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlPedagioCTRB |
| 15 | SaldoCTRB | numeric | p=38, s=4 | VW_CONTROLADORIA_DE_FRETE.SaldoCTRB |
| 16 | QtPeso | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.QtPeso |
| 17 | VlLiquido | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.VlLiquido |

### Configuracoes de Erro do Destino

| Configuracao | Valor |
|-------------|-------|
| errorRowDisposition (input) | **IgnoreFailure** — erros de linha sao ignorados! |
| Error Output | Disponivel (ErrorCode + ErrorColumn) mas nao conectado |

> Atencao critica: O destino usa `IgnoreFailure` no input. Isso significa que linhas com erro de insercao sao silenciosamente descartadas sem nenhum log.

## Tabela Fisica Destino: `fControladoria`

A insercao e realizada via view `VW_CONTROLADORIA_BASE_EMISSAO`, mas a limpeza e feita diretamente na tabela:
```sql
DELETE FROM [DBStage].[dbo].[fControladoria]
```

Isso confirma que a view e um alias direto (ou INSTEAD OF trigger) da tabela `fControladoria`.
