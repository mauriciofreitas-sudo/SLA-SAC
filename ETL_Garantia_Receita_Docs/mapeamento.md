# ETL_Garantia_Receita — Mapeamento de Colunas

## Data Flow: Tarefa Fluxo de Dados
### Mapeamento Completo: vw_GARANTIA_RECEITA_V2 → GARANTIA_RECEITA

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Grupo |
|---|---|---|---|---|
| 1 | Pto Operação | str | 10 | Empresa |
| 2 | Cte | i4 | — | Identificacao |
| 3 | Tipo de Documento | i4 | — | Classificacao |
| 4 | Emissão | dbTimeStamp | — | Data |
| 5 | Cliente | str | 100 | Cliente |
| 6 | Total Prestação | numeric | p14,s4 | Valor |
| 7 | Total Frete | numeric | p14,s4 | Valor |
| 8 | Situação | str | 29 | Status |
| 9 | Empresa Fatura | i4 | — | Faturamento |
| 10 | Fatura | i4 | — | Faturamento |
| 11 | Dt Emissão Fatura | dbTimeStamp | — | Data |
| 12 | Dt Vencimento | dbTimeStamp | — | Data |
| 13 | Valor Total da Fatura | numeric | p14,s4 | Valor |
| 14 | Dt Pagamento | dbTimeStamp | — | Data |
| 15 | Valor Pago | numeric | p14,s4 | Valor |
| 16 | Acréscimos | numeric | p14,s4 | Valor |
| 17 | Deduções | numeric | p14,s4 | Valor |
| 18 | Saldo | numeric | p14,s4 | Valor |
| 19 | Status | str | 17 | Status |
| 20 | Remetente | str | 100 | Entidade |
| 21 | Destinatário | str | 100 | Entidade |
| 22 | Tipo de Transporte | str | 40 | Transporte |
| 23 | Natureza de Carga | str | 40 | Transporte |
| 24 | Conteúdo (CFOP) | str | 10 | Fiscal |
| 25 | Nota Fiscal | str | 255 | Documento |
| 26 | Vl Total Notas | numeric | p14,s4 | Valor |
| 27 | Cidade Origem | str | 30 | Localizacao |
| 28 | Cidade Destino | str | 30 | Localizacao |
| 29 | Peso Cub. | numeric | p14,s4 | Operacional |
| 30 | Frete Peso | numeric | p14,s4 | Valor |
| 31 | Frete Valor | numeric | p14,s4 | Valor |
| 32 | GRIS | numeric | p14,s4 | Valor |
| 33 | Pedágio | numeric | p14,s4 | Valor |
| 34 | Recebedor | str | 100 | Entidade |
| 35 | Tipo Emissão | str | 16 | Classificacao |
| 36 | CNPJ Cliente | str | 14 | Fiscal |
| 37 | CNPJ Destinatário | str | 14 | Fiscal |
| 38 | Origem UF | str | 3 | Localizacao |
| 39 | Destino UF | str | 3 | Localizacao |
| 40 | Arquivo CONEMB | str | 255 | EDI |
| 41 | Arquivo DOCCOB | str | 255 | EDI |
| 42 | CTe Substituido | str | 3/5 | Classificacao |
| 43 | Dt Entrega | dbTimeStamp | — | Data |
| 44 | Status Pré Protocolo | str | 12 | Status |
| 45 | Vendor | str | 13 | Entidade |
| 46 | Evento | str | 30 | Rastreamento |
| 47 | Status Entrega | str | 50 | Status |

**Total de colunas mapeadas: 47**

## Distribuicao por Grupo

```
Grupo          | Qtd Colunas
---------------|------------
Valor          |   12
Data           |    5
Status         |    3
Entidade       |    4
Localizacao    |    4
Identificacao  |    1
Classificacao  |    4
Empresa        |    1
Cliente        |    1
Faturamento    |    2
Transporte     |    2
Fiscal         |    3
Documento      |    1
Operacional    |    1
EDI            |    2
Rastreamento   |    1
---------------|------------
TOTAL          |   47
```

## Totais por Tipo de Dado SSIS

| Tipo | Qtd |
|---|---|
| str | 27 |
| numeric | 13 |
| i4 | 4 |
| dbTimeStamp | 5 |
| (menos 2 confliitos tipo/tamanho) | — |
