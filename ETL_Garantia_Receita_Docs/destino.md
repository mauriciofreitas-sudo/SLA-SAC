# ETL_Garantia_Receita — Destino de Dados

## Servidor de Destino

| Campo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |
| Connection Manager | DBSTAGE (OLEDB SQLOLEDB.1) |

## Data Flow: Tarefa Fluxo de Dados

### Componente Destination

| Campo | Valor |
|---|---|
| Nome | Destino OLE DB |
| Tipo | OLE DB Destination |
| Tabela de Destino | `[dbo].[GARANTIA_RECEITA]` |
| AccessMode | 3 (FastLoad) |
| CommandTimeout | 0 (infinito) |
| FastLoadKeepIdentity | false |
| FastLoadKeepNulls | false |
| FastLoadOptions | TABLOCK,CHECK_CONSTRAINTS |
| FastLoadMaxInsertCommitSize | 2147483647 |
| DefaultCodePage | 1252 |
| errorOrTruncationOperation | Inserir |
| errorRowDisposition | FailComponent |

## Estrategia de Carga

**Modo: DELETE + Reload (Full)**

1. Task `Delete Table` executa `delete from [dbo].[GARANTIA_RECEITA]` — apaga todos os registros
2. Task `Tarefa Fluxo de Dados` carrega todos os registros da view de origem

## Pre-processamento

```sql
delete from [dbo].[GARANTIA_RECEITA]
```

## Schema de Destino — Tabela GARANTIA_RECEITA (47 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Codepage Destino |
|---|---|---|---|---|
| 1 | Pto Operação | str | 10 | 1252 |
| 2 | Cte | i4 | — | — |
| 3 | Tipo de Documento | i4 | — | — |
| 4 | Emissão | dbTimeStamp | — | — |
| 5 | Cliente | str | 100 | 1252 |
| 6 | Total Prestação | numeric | p14,s4 | — |
| 7 | Total Frete | numeric | p14,s4 | — |
| 8 | Situação | str | 29 | 1252 |
| 9 | Empresa Fatura | i4 | — | — |
| 10 | Fatura | i4 | — | — |
| 11 | Dt Emissão Fatura | dbTimeStamp | — | — |
| 12 | Dt Vencimento | dbTimeStamp | — | — |
| 13 | Valor Total da Fatura | numeric | p14,s4 | — |
| 14 | Dt Pagamento | dbTimeStamp | — | — |
| 15 | Valor Pago | numeric | p14,s4 | — |
| 16 | Acréscimos | numeric | p14,s4 | — |
| 17 | Deduções | numeric | p14,s4 | — |
| 18 | Saldo | numeric | p14,s4 | — |
| 19 | Status | str | 17 | 1252 |
| 20 | Remetente | str | 100 | 1252 |
| 21 | Destinatário | str | 100 | 1252 |
| 22 | Tipo de Transporte | str | 40 | 1252 |
| 23 | Natureza de Carga | str | 40 | 1252 |
| 24 | Conteúdo (CFOP) | str | 10 | 1252 |
| 25 | Nota Fiscal | str | 255 | 1252 |
| 26 | Vl Total Notas | numeric | p14,s4 | — |
| 27 | Cidade Origem | str | 30 | 1252 |
| 28 | Cidade Destino | str | 30 | 1252 |
| 29 | Peso Cub. | numeric | p14,s4 | — |
| 30 | Frete Peso | numeric | p14,s4 | — |
| 31 | Frete Valor | numeric | p14,s4 | — |
| 32 | GRIS | numeric | p14,s4 (destino p15,s2) | — |
| 33 | Pedágio | numeric | p14,s4 (destino p15,s2) | — |
| 34 | Recebedor | str | 100 | 1252 |
| 35 | Tipo Emissão | str | 16 | 1252 |
| 36 | CNPJ Cliente | str | 14 | 1252 |
| 37 | CNPJ Destinatário | str | 14 | 1252 |
| 38 | Origem UF | str | 3 | 1252 |
| 39 | Destino UF | str | 3 | 1252 |
| 40 | Arquivo CONEMB | str | 255 | 1252 |
| 41 | Arquivo DOCCOB | str | 255 | 1252 |
| 42 | CTe Substituido | str | 5 (destino 5) | 1252 |
| 43 | Dt Entrega | dbTimeStamp | — | — |
| 44 | Status Pré Protocolo | str | 12 | 1252 |
| 45 | Vendor | str | 13 | 1252 |
| 46 | Evento | str | 30 | 1252 |
| 47 | Status Entrega | str | 50 | 1252 |

**Total: 47 colunas**
