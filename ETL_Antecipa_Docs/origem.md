# Origem dos Dados — ETL_Antecipa

## Identificação do Servidor de Origem

| Propriedade | Valor |
|-------------|-------|
| Servidor | 169.57.181.231 |
| Banco de Dados | SOFTRAN_TRANSLUTE |
| Usuário | softran |
| Tipo de Conexão | ADO.NET — System.Data.SqlClient |
| Connection Manager | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc |
| TrustServerCertificate | True |

---

## Data Flow 1: Load Fornecedores

### ADO NET Source — `VW_Int_Pessoa`

| Propriedade | Valor |
|-------------|-------|
| Objeto de origem | `"dbo"."VW_Int_Pessoa"` |
| Modo de acesso | AccessMode = 0 (TableOrViewName) |
| SqlCommand | *(vazio — leitura direta da view)* |
| CommandTimeout | 30 segundos |
| AllowImplicitStringConversion | true |

### Colunas Extraídas — `VW_Int_Pessoa`

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|---|--------|-----------|---------|-----------|
| 1 | CODIGO | wstr | 14 | Código identificador do fornecedor/pessoa |
| 2 | NOME | wstr | 100 | Nome completo da pessoa/empresa |
| 3 | TIPO | wstr | 1 | Tipo de pessoa (F=Física, J=Jurídica) |
| 4 | Email | wstr | 100 | Endereço de e-mail |

> Observação: O destino `fFornecedoresSenior` possui colunas adicionais de log (`logs_dt_envio`, `logs_sts_envio`) que não são mapeadas na source — provavelmente preenchidas por valor padrão ou NULL.

---

## Data Flow 2: Load Notas

### ADO NET Source — `VW_int_s_LuCash`

| Propriedade | Valor |
|-------------|-------|
| Objeto de origem | `"dbo"."VW_int_s_LuCash"` |
| Modo de acesso | AccessMode = 0 (TableOrViewName) |
| SqlCommand | *(vazio — leitura direta da view)* |
| CommandTimeout | 30 segundos |
| AllowImplicitStringConversion | true |

### Colunas Extraídas — `VW_int_s_LuCash`

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|---|--------|-----------|---------|-----------|
| 1 | Origem | wstr | 30 | Sistema de origem do título |
| 2 | StatusPagto | wstr | 29 | Status do pagamento |
| 3 | CNPJFornecedor | wstr | 14 | CNPJ do fornecedor (sem máscara) |
| 4 | CNPJComprador | wstr | 18 | CNPJ do comprador (com máscara?) |
| 5 | ChaveNotaFiscal | wstr | 50 | Chave de acesso da NF-e |
| 6 | TipoNF | wstr | 1 | Tipo de nota fiscal |
| 7 | NF | nText | MAX (2147483647) | Número da nota fiscal |
| 8 | SerieNotaFiscal | nText | MAX (2147483647) | Série da nota fiscal |
| 9 | Titulo | wstr | 15 | Número do título |
| 10 | QtdTitulo | i4 (Int) | — | Quantidade de títulos |
| 11 | Valor | numeric | prec=14, scale=4 | Valor do título |
| 12 | VlDesconto | numeric | prec=14, scale=4 | Valor do desconto |
| 13 | DtEmissao | dbTimeStamp | — | Data de emissão |
| 14 | DtVencimento | dbTimeStamp | — | Data de vencimento |
| 15 | StatusFatura | wstr | 4 | Status da fatura |

> Observação: O destino `fNfSenior` possui colunas adicionais de log (`log_dt_envio`, `log_sts_envio`) que não são mapeadas na source.
