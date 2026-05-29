# Mapeamento de Colunas — ETL_Antecipa

## Visão Geral

| Data Flow | Source | Destination | Colunas Source | Colunas Destino | Transformações |
|-----------|--------|-------------|----------------|-----------------|----------------|
| Load Fornecedores | VW_Int_Pessoa | fFornecedoresSenior | 4 | 7 (4 mapeadas) | Nenhuma |
| Load Notas | VW_int_s_LuCash | fNfSenior | 15 | 18 (15 mapeadas) | Nenhuma |

---

## Mapeamento Completo — Load Fornecedores

| # | Coluna | Tipo SSIS | Tamanho | Origem | Grupo |
|---|--------|-----------|---------|--------|-------|
| 1 | CODIGO | wstr | 14 | VW_Int_Pessoa.CODIGO | Identificacao |
| 2 | NOME | wstr | 100 | VW_Int_Pessoa.NOME | Cadastro |
| 3 | TIPO | wstr | 1 | VW_Int_Pessoa.TIPO | Cadastro |
| 4 | Email | wstr | 100 | VW_Int_Pessoa.Email | Contato |

---

## Mapeamento Completo — Load Notas

| # | Coluna | Tipo SSIS | Tamanho | Origem | Grupo |
|---|--------|-----------|---------|--------|-------|
| 1 | Origem | wstr | 30 | VW_int_s_LuCash.Origem | Identificacao |
| 2 | StatusPagto | wstr | 29 | VW_int_s_LuCash.StatusPagto | Status |
| 3 | CNPJFornecedor | wstr | 14 | VW_int_s_LuCash.CNPJFornecedor | Participantes |
| 4 | CNPJComprador | wstr | 18 | VW_int_s_LuCash.CNPJComprador | Participantes |
| 5 | ChaveNotaFiscal | wstr | 50 | VW_int_s_LuCash.ChaveNotaFiscal | Nota Fiscal |
| 6 | TipoNF | wstr | 1 | VW_int_s_LuCash.TipoNF | Nota Fiscal |
| 7 | NF | nText | MAX | VW_int_s_LuCash.NF | Nota Fiscal |
| 8 | SerieNotaFiscal | nText | MAX | VW_int_s_LuCash.SerieNotaFiscal | Nota Fiscal |
| 9 | Titulo | wstr | 15 | VW_int_s_LuCash.Titulo | Titulo |
| 10 | QtdTitulo | i4 | — | VW_int_s_LuCash.QtdTitulo | Titulo |
| 11 | Valor | numeric | p=14 s=4 | VW_int_s_LuCash.Valor | Financeiro |
| 12 | VlDesconto | numeric | p=14 s=4 | VW_int_s_LuCash.VlDesconto | Financeiro |
| 13 | DtEmissao | dbTimeStamp | — | VW_int_s_LuCash.DtEmissao | Temporal |
| 14 | DtVencimento | dbTimeStamp | — | VW_int_s_LuCash.DtVencimento | Temporal |
| 15 | StatusFatura | wstr | 4 | VW_int_s_LuCash.StatusFatura | Status |

---

## Distribuição por Grupo — Load Notas

```
Identificacao [#] 1 coluna  |######|   7%
Status        [#] 2 colunas |############|  13%
Participantes [#] 2 colunas |############|  13%
Nota Fiscal   [#] 4 colunas |########################|  27%
Titulo        [#] 2 colunas |############|  13%
Financeiro    [#] 2 colunas |############|  13%
Temporal      [#] 2 colunas |############|  13%
                                            Total: 15 colunas
```

---

## Tipos de Dados — Resumo

| Tipo SSIS | Equivalente SQL Server | Ocorrencias |
|-----------|----------------------|-------------|
| wstr | NVARCHAR | 10 colunas (Load Notas) + 4 (Load Fornecedores) |
| nText | NTEXT | 2 colunas (NF, SerieNotaFiscal) |
| numeric | DECIMAL | 2 colunas (Valor, VlDesconto) |
| dbTimeStamp | DATETIME | 2 colunas (DtEmissao, DtVencimento) |
| i4 | INT | 1 coluna (QtdTitulo) |
