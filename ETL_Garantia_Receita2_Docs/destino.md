# ETL_Garantia_Receita2 — Destino de Dados

## Servidor de Destino

| Campo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DBStage |
| Usuario | sqldba |
| Connection Manager | 10.100.86.89.DBStage.sqldba (ADO.NET SqlClient) |

## Data Flow: Load fCTE

### Componente Destination

| Campo | Valor |
|---|---|
| Nome | ADO NET Destination |
| Tipo | ADO.NET Destination (Microsoft.ADONETDestination) |
| Tabela de Destino | `"dbo"."GARANTIA_RECEITA"` |
| BatchSize | 0 (usa buffer interno SSIS) |
| CommandTimeout | 600 segundos |
| UseBulkInsertWhenPossible | true (SqlBulkCopy) |
| validateExternalMetadata | False |
| errorRowDisposition | FailComponent |

## Estrategia de Carga

**Modo: TRUNCATE + Reload (Full)**

1. Task `Truncate fGarantia_Receita` executa `TRUNCATE TABLE "GARANTIA_RECEITA"` (mais rapido que DELETE)
2. Task `Load fCTE` carrega todos os registros da view de origem via ADO.NET/SqlBulkCopy

## Pre-processamento

```sql
TRUNCATE TABLE "GARANTIA_RECEITA"
```

## Schema de Destino — Tabela GARANTIA_RECEITA (48 colunas — v2)

| # | Coluna | Tipo SSIS (destino) | Tamanho/Precisao | Diferenca vs v1 |
|---|---|---|---|---|
| 1 | Pto Operação | wstr | 10 | — |
| 2 | Cte | i4 | — | — |
| 3 | Tipo de Documento | wstr | 40 | MUDOU: era i4, agora wstr(40) |
| 4 | Emissão | dbTimeStamp | — | — |
| 5 | Cliente | wstr | 100 | — |
| 6 | Total Prestação | numeric | p15,s2 | — |
| 7 | Total Frete | numeric | p15,s2 | — |
| 8 | Situação | wstr | 29 | — |
| 9 | Empresa Fatura | i4 | — | — |
| 10 | CdTitulo | wstr | 15 | NOVO — nao existe no v1 |
| 11 | Fatura | i4 | — | — |
| 12 | Dt Emissão Fatura | dbTimeStamp | — | — |
| 13 | Dt Vencimento | dbTimeStamp | — | — |
| 14 | Valor Total da Fatura | numeric | p15,s2 | — |
| 15 | Dt Pagamento | dbTimeStamp | — | — |
| 16 | Valor Pago | numeric | p15,s2 | — |
| 17 | Acréscimos | numeric | p15,s2 | — |
| 18 | Deduções | numeric | p15,s2 | — |
| 19 | Saldo | numeric | p15,s2 | — |
| 20 | Status | wstr | 17 | — |
| 21 | Remetente | wstr | 100 | — |
| 22 | Destinatário | wstr | 100 | — |
| 23 | Tipo de Transporte | wstr | 40 | — |
| 24 | Natureza de Carga | wstr | 40 | — |
| 25 | Conteúdo (CFOP) | wstr | 10 | — |
| 26 | Nota Fiscal | wstr | 255 | — |
| 27 | Vl Total Notas | numeric | p14,s4 | — |
| 28 | Cidade Origem | wstr | 30 | — |
| 29 | Cidade Destino | wstr | 30 | — |
| 30 | Peso Cub. | numeric | p14,s4 | — |
| 31 | Frete Peso | numeric | p14,s4 | — |
| 32 | Frete Valor | numeric | p14,s4 | — |
| 33 | GRIS | numeric | p14,s4 | — |
| 34 | Pedágio | numeric | p14,s4 | — |
| 35 | Recebedor | wstr | 100 | — |
| 36 | Tipo Emissão | wstr | 16 | — |
| 37 | CNPJ Cliente | wstr | 14 | — |
| 38 | CNPJ Destinatário | wstr | 14 | — |
| 39 | Origem UF | wstr | 2 | Corrigido: era 3 no v1 |
| 40 | Destino UF | wstr | 2 | Corrigido: era 3 no v1 |
| 41 | Arquivo CONEMB | wstr | 255 | — |
| 42 | Arquivo DOCCOB | wstr | 255 | — |
| 43 | CTe Substituido | wstr | 3 | — |
| 44 | Dt Entrega | dbTimeStamp | — | — |
| 45 | Status Pré Protocolo | wstr | 12 | — |
| 46 | Vendor | wstr | 13 | — |
| 47 | Evento | wstr | 30 | — |
| 48 | Status Entrega | wstr | 50 | — |

**Total: 48 colunas**

> Nota: A tabela GARANTIA_RECEITA no banco deve ter sido alterada para incluir CdTitulo e mudar Tipo de Documento de INT para VARCHAR(40) antes de usar este package v2.
