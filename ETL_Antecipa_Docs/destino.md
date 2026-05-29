# Destino dos Dados — ETL_Antecipa

## Identificação do Servidor de Destino

| Propriedade | Valor |
|-------------|-------|
| Servidor | 10.100.86.89 |
| Banco de Dados | DBStage |
| Usuário | sqldba |
| Tipo de Conexão | ADO.NET — System.Data.SqlClient |
| Connection Manager | 10.100.86.89.DBStage.sqldba 1 |

---

## Destino 1: `fFornecedoresSenior`

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `"fFornecedoresSenior"` |
| BatchSize | 0 (buffer SSIS padrão) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true |
| Estratégia de carga | **Full Replace** — DELETE antes do INSERT (via "Truncate Fornecedores") |

### Colunas no Destino — `fFornecedoresSenior`

| # | Coluna | Tipo SSIS | Tamanho | Mapeado? | Origem |
|---|--------|-----------|---------|----------|--------|
| 1 | ID | i4 (Int) | — | Nao | Identidade/gerado pelo banco |
| 2 | CODIGO | wstr | 14 | Sim | VW_Int_Pessoa.CODIGO |
| 3 | NOME | wstr | 100 | Sim | VW_Int_Pessoa.NOME |
| 4 | TIPO | wstr | 1 | Sim | VW_Int_Pessoa.TIPO |
| 5 | Email | wstr | 100 | Sim | VW_Int_Pessoa.Email |
| 6 | logs_dt_envio | dbTimeStamp | — | Nao | NULL/default |
| 7 | logs_sts_envio | wstr | 30 | Nao | NULL/default |

---

## Destino 2: `fNfSenior`

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `"fNfSenior"` |
| BatchSize | 0 (buffer SSIS padrão) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true |
| Estratégia de carga | **Full Replace** — DELETE antes do INSERT (via "Truncate Notas") |

### Colunas no Destino — `fNfSenior`

| # | Coluna | Tipo SSIS | Tamanho | Mapeado? | Origem |
|---|--------|-----------|---------|----------|--------|
| 1 | ID | i4 (Int) | — | Nao | Identidade/gerado pelo banco |
| 2 | Origem | wstr | 30 | Sim | VW_int_s_LuCash.Origem |
| 3 | StatusPagto | wstr | 29 | Sim | VW_int_s_LuCash.StatusPagto |
| 4 | CNPJFornecedor | wstr | 14 | Sim | VW_int_s_LuCash.CNPJFornecedor |
| 5 | CNPJComprador | wstr | 18 | Sim | VW_int_s_LuCash.CNPJComprador |
| 6 | ChaveNotaFiscal | wstr | 50 | Sim | VW_int_s_LuCash.ChaveNotaFiscal |
| 7 | TipoNF | wstr | 1 | Sim | VW_int_s_LuCash.TipoNF |
| 8 | NF | wstr | MAX | Sim | VW_int_s_LuCash.NF |
| 9 | SerieNotaFiscal | wstr | MAX | Sim | VW_int_s_LuCash.SerieNotaFiscal |
| 10 | Titulo | wstr | 15 | Sim | VW_int_s_LuCash.Titulo |
| 11 | QtdTitulo | i4 | — | Sim | VW_int_s_LuCash.QtdTitulo |
| 12 | Valor | numeric | p=14 s=4 | Sim | VW_int_s_LuCash.Valor |
| 13 | VlDesconto | numeric | p=14 s=4 | Sim | VW_int_s_LuCash.VlDesconto |
| 14 | DtEmissao | dbTimeStamp | — | Sim | VW_int_s_LuCash.DtEmissao |
| 15 | DtVencimento | dbTimeStamp | — | Sim | VW_int_s_LuCash.DtVencimento |
| 16 | StatusFatura | wstr | 4 | Sim | VW_int_s_LuCash.StatusFatura |
| 17 | log_dt_envio | dbTimeStamp | — | Nao | NULL/default |
| 18 | log_sts_envio | wstr | 30 | Nao | NULL/default |
