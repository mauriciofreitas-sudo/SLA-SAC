# ETL_Garantia_Receita2 — Mapeamento de Colunas

## Data Flow: Load fCTE
### Mapeamento Completo: vw_GARANTIA_RECEITA_V2 → GARANTIA_RECEITA

| # | Coluna | Tipo Origem | Tipo Destino (external) | Tamanho | Grupo | Status vs v1 |
|---|---|---|---|---|---|---|
| 1 | Pto Operação | wstr | wstr | 10 | Empresa | = |
| 2 | Cte | i4 | i4 | — | Identificacao | = |
| 3 | Tipo de Documento | i4 | wstr | 40 | Classificacao | TIPO MUDOU |
| 4 | Emissão | dbTimeStamp | dbTimeStamp | — | Data | = |
| 5 | Cliente | wstr | wstr | 100 | Cliente | = |
| 6 | Total Prestação | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 7 | Total Frete | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 8 | Situação | wstr | wstr | 29 | Status | = |
| 9 | Empresa Fatura | i4 | i4 | — | Faturamento | = |
| 10 | CdTitulo | wstr | wstr | 15 | Faturamento | NOVO |
| 11 | Fatura | i4 | i4 | — | Faturamento | = |
| 12 | Dt Emissão Fatura | dbTimeStamp | dbTimeStamp | — | Data | = |
| 13 | Dt Vencimento | dbTimeStamp | dbTimeStamp | — | Data | = |
| 14 | Valor Total da Fatura | numeric p38,s4 | numeric p15,s2 | — | Valor | = |
| 15 | Dt Pagamento | dbTimeStamp | dbTimeStamp | — | Data | = |
| 16 | Valor Pago | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 17 | Acréscimos | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 18 | Deduções | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 19 | Saldo | numeric p14,s4 | numeric p15,s2 | — | Valor | = |
| 20 | Status | wstr | wstr | 17 | Status | = |
| 21 | Remetente | wstr | wstr | 100 | Entidade | = |
| 22 | Destinatário | wstr | wstr | 100 | Entidade | = |
| 23 | Tipo de Transporte | wstr | wstr | 40 | Transporte | = |
| 24 | Natureza de Carga | wstr | wstr | 40 | Transporte | = |
| 25 | Conteúdo (CFOP) | wstr | wstr | 10 | Fiscal | = |
| 26 | Nota Fiscal | wstr | wstr | 255 | Documento | = |
| 27 | Vl Total Notas | numeric p14,s4 | numeric p14,s4 | — | Valor | = |
| 28 | Cidade Origem | wstr | wstr | 30 | Localizacao | = |
| 29 | Cidade Destino | wstr | wstr | 30 | Localizacao | = |
| 30 | Peso Cub. | numeric p14,s4 | numeric p14,s4 | — | Operacional | = |
| 31 | Frete Peso | numeric p14,s4 | numeric p14,s4 | — | Valor | = |
| 32 | Frete Valor | numeric p14,s4 | numeric p14,s4 | — | Valor | = |
| 33 | GRIS | numeric p14,s4 | numeric p14,s4 | — | Valor | = |
| 34 | Pedágio | numeric p14,s4 | numeric p14,s4 | — | Valor | = |
| 35 | Recebedor | wstr | wstr | 100 | Entidade | = |
| 36 | Tipo Emissão | wstr | wstr | 16 | Classificacao | = |
| 37 | CNPJ Cliente | wstr | wstr | 14 | Fiscal | = |
| 38 | CNPJ Destinatário | wstr | wstr | 14 | Fiscal | = |
| 39 | Origem UF | wstr | wstr | 2 | Localizacao | CORRIGIDO (era 3) |
| 40 | Destino UF | wstr | wstr | 2 | Localizacao | CORRIGIDO (era 3) |
| 41 | Arquivo CONEMB | wstr | wstr | 255 | EDI | = |
| 42 | Arquivo DOCCOB | wstr | wstr | 255 | EDI | = |
| 43 | CTe Substituido | wstr | wstr | 3 | Classificacao | = |
| 44 | Dt Entrega | dbTimeStamp | dbTimeStamp | — | Data | = |
| 45 | Status Pré Protocolo | wstr | wstr | 12 | Status | = |
| 46 | Vendor | wstr | wstr | 13 | Entidade | = |
| 47 | Evento | wstr | wstr | 30 | Rastreamento | = |
| 48 | Status Entrega | wstr | wstr | 50 | Status | = |

**Total de colunas mapeadas: 48**

## Distribuicao por Grupo

```
Grupo          | Qtd Colunas
---------------|------------
Valor          |   13
Data           |    5
Status         |    3
Entidade       |    4
Localizacao    |    4
Identificacao  |    1
Classificacao  |    4
Empresa        |    1
Cliente        |    1
Faturamento    |    3
Transporte     |    2
Fiscal         |    3
Documento      |    1
Operacional    |    1
EDI            |    2
Rastreamento   |    1
---------------|------------
TOTAL          |   48
```

## Totais por Tipo de Dado SSIS (Origem)

| Tipo | Qtd |
|---|---|
| wstr | 28 |
| numeric | 15 |
| i4 | 3 |
| dbTimeStamp | 5 |

## Resumo das Mudancas vs ETL_Garantia_Receita (v1)

| Tipo de Mudanca | Coluna | Detalhe |
|---|---|---|
| ADICIONADA | CdTitulo | wstr(15) — titulo financeiro vinculado |
| TIPO MUDOU | Tipo de Documento | i4 → wstr(40) — agora textual |
| CORRIGIDO | Origem UF | str(3) → wstr(2) — tamanho correto para UF |
| CORRIGIDO | Destino UF | str(3) → wstr(2) — tamanho correto para UF |
| PADRAO MUDOU | Todos os campos texto | str (ANSI/1252) → wstr (Unicode) |
