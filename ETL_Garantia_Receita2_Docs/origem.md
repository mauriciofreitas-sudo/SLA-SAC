# ETL_Garantia_Receita2 — Origem de Dados

## Servidor de Origem

| Campo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | SOFTRAN_TRANSLUTE |
| Usuario | softran |
| Connection Manager | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc (ADO.NET SqlClient) |
| CommandTimeout | 600 segundos |
| AccessMode | 0 (OpenRowset — leitura direta da view) |

## Data Flow: Load fCTE

### Componente Source

| Campo | Valor |
|---|---|
| Nome | ADO NET Source |
| Tipo | ADO.NET Source (Microsoft.DataReaderSourceAdapter) |
| Objeto/View | `"dbo"."vw_GARANTIA_RECEITA_V2"` |
| SQL no SqlCommand (pre-visualizacao) | (mesma query complexa do ETL_Garantia_Receita2 — usa SOFTRAN_TRANSLUTE) |
| AccessMode | 0 (OpenRowset) |
| AllowImplicitStringConversion | true |

### Query SQL (utilizada para pre-visualizacao/metadados)

A query SQL completa esta registrada no SqlCommand do componente e e a mesma query de emissao de CTe (usando `gtcconhe` como tabela principal com multiplos JOINs e OUTER APPLYs), identica em estrutura a do ETL_Garantia_Receita mas com o filtro `a.dtemissao >= '20240101'` aplicado.

### Colunas de Saida — ADO NET Source Output

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Observacao |
|---|---|---|---|---|
| 1 | Pto Operação | wstr | 10 | Ponto de operacao/filial |
| 2 | Cte | i4 | — | Numero do CTe |
| 3 | Tipo de Documento | i4 | — | Codigo do tipo de documento |
| 4 | Emissão | dbTimeStamp | — | Data de emissao |
| 5 | Cliente | wstr | 100 | Nome do cliente pagador |
| 6 | Total Prestação | numeric | p14,s4 | Valor total da prestacao |
| 7 | Total Frete | numeric | p14,s4 | Valor total do frete |
| 8 | Situação | wstr | 29 | Situacao do CTe |
| 9 | Empresa Fatura | i4 | — | Codigo empresa da fatura |
| 10 | CdTitulo | wstr | 15 | Codigo do titulo financeiro (NOVO vs v1) |
| 11 | Fatura | i4 | — | Numero da fatura |
| 12 | Dt Emissão Fatura | dbTimeStamp | — | Data de emissao da fatura |
| 13 | Dt Vencimento | dbTimeStamp | — | Data de vencimento |
| 14 | Valor Total da Fatura | numeric | p38,s4 | Valor total da fatura |
| 15 | Dt Pagamento | dbTimeStamp | — | Data de pagamento |
| 16 | Valor Pago | numeric | p14,s4 | Valor efetivamente pago |
| 17 | Acréscimos | numeric | p14,s4 | Acrescimos/juros |
| 18 | Saldo | numeric | p14,s4 | Saldo a receber |
| 19 | Deduções | numeric | p14,s4 | Deducoes/descontos |
| 20 | Status | wstr | 17 | Status do CTe |
| 21 | Remetente | wstr | 100 | Nome do remetente |
| 22 | Destinatário | wstr | 100 | Nome do destinatario |
| 23 | Tipo de Transporte | wstr | 40 | Tipo de transporte |
| 24 | Natureza de Carga | wstr | 40 | Natureza da carga |
| 25 | Conteúdo (CFOP) | wstr | 10 | CFOP da nota fiscal |
| 26 | Nota Fiscal | wstr | 255 | Numero(s) da(s) nota(s) fiscal(is) |
| 27 | Vl Total Notas | numeric | p14,s4 | Valor total das notas fiscais |
| 28 | Cidade Origem | wstr | 30 | Cidade de origem |
| 29 | Cidade Destino | wstr | 30 | Cidade de destino |
| 30 | Peso Cub. | numeric | p14,s4 | Peso cubado |
| 31 | Frete Peso | numeric | p14,s4 | Frete calculado por peso |
| 32 | Frete Valor | numeric | p14,s4 | Frete calculado por valor |
| 33 | GRIS | numeric | p14,s4 | Taxa GRIS |
| 34 | Pedágio | numeric | p14,s4 | Valor do pedagio |
| 35 | Recebedor | wstr | 100 | Nome do recebedor |
| 36 | Tipo Emissão | wstr | 16 | Tipo de emissao do CTe |
| 37 | CNPJ Cliente | wstr | 14 | CNPJ do cliente |
| 38 | CNPJ Destinatário | wstr | 14 | CNPJ do destinatario |
| 39 | Origem UF | wstr | 2 | UF de origem |
| 40 | Destino UF | wstr | 2 | UF de destino |
| 41 | Arquivo CONEMB | wstr | 255 | Arquivo EDI CONEMB |
| 42 | Arquivo DOCCOB | wstr | 255 | Arquivo EDI DOCCOB |
| 43 | CTe Substituido | wstr | 3 | Indicador se CTe foi substituido |
| 44 | Dt Entrega | dbTimeStamp | — | Data de entrega |
| 45 | Status Pré Protocolo | wstr | 12 | Status antes do protocolo |
| 46 | Vendor | wstr | 13 | Codigo do vendor/fornecedor |
| 47 | Evento | wstr | 30 | Ultimo evento de rastreamento |
| 48 | Status Entrega | wstr | 50 | Status de entrega |

**Total de colunas: 48 (47 do v1 + CdTitulo)**

### Diferenca vs ETL_Garantia_Receita (v1)

- Coluna **CdTitulo** (wstr,15) adicionada na posicao 10
- Colunas de string usam **wstr** (Unicode) em vez de **str** (ANSI) — provider ADO.NET vs OLE DB
- **Origem UF** e **Destino UF**: tamanho 2 (corrigido vs v1 que tinha 3)
