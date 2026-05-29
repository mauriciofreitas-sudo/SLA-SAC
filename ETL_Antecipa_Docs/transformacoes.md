# Transformações — ETL_Antecipa

## Resumo

Este package é **passthrough puro**: não há nenhum componente de transformação (Derived Column, Lookup, Data Conversion, etc.) nos Data Flow Tasks. Os dados fluem diretamente da source para o destino sem nenhuma modificação.

---

## Componentes de Cada Data Flow

### Data Flow 1: Load Fornecedores

```
+------------------+      +----------------------+
|  ADO NET Source  |      |  ADO NET Destination  |
|                  |      |                       |
|  VW_Int_Pessoa   | ---> |  fFornecedoresSenior  |
|  (4 colunas)     |      |  (4 colunas mapeadas) |
+------------------+      +----------------------+
      |                          |
      v (Error Output)           v (Error Output)
  [não conectado]          [não conectado]
```

### Data Flow 2: Load Notas

```
+------------------+      +----------------------+
|  ADO NET Source  |      |  ADO NET Destination  |
|                  |      |                       |
| VW_int_s_LuCash  | ---> |  fNfSenior            |
|  (15 colunas)    |      |  (15 colunas mapeadas)|
+------------------+      +----------------------+
      |                          |
      v (Error Output)           v (Error Output)
  [não conectado]          [não conectado]
```

---

## Pré-processamento (SQL Tasks antes dos Data Flows)

| Ordem | Task | SQL Executado | Conexão |
|-------|------|---------------|---------|
| 1 | Truncate Fornecedores | `DELETE FROM fFornecedoresSenior` | DBStage (10.100.86.89) |
| 2 | Load Fornecedores | *(Data Flow)* | — |
| 3 | Truncate Notas | `DELETE FROM dbo.fNfSenior` | DBStage (10.100.86.89) |
| 4 | Load Notas | *(Data Flow)* | — |

> Nota: As tasks usam `DELETE` e não `TRUNCATE`. Isso é mais lento e gera mais log de transação. Para tabelas grandes, `TRUNCATE TABLE` seria mais eficiente (se não houver FKs).

---

## Mapeamento de Tipos de Dados

### Load Fornecedores

| Coluna | Tipo Source (SSIS) | Tipo Destino (SSIS) | Conversão |
|--------|-------------------|---------------------|-----------|
| CODIGO | wstr(14) | wstr(14) | Nenhuma |
| NOME | wstr(100) | wstr(100) | Nenhuma |
| TIPO | wstr(1) | wstr(1) | Nenhuma |
| Email | wstr(100) | wstr(100) | Nenhuma |

### Load Notas

| Coluna | Tipo Source (SSIS) | Tipo Destino (SSIS) | Conversão |
|--------|-------------------|---------------------|-----------|
| Origem | wstr(30) | wstr(30) | Nenhuma |
| StatusPagto | wstr(29) | wstr(29) | Nenhuma |
| CNPJFornecedor | wstr(14) | wstr(14) | Nenhuma |
| CNPJComprador | wstr(18) | wstr(18) | Nenhuma |
| ChaveNotaFiscal | wstr(50) | wstr(50) | Nenhuma |
| TipoNF | wstr(1) | wstr(1) | Nenhuma |
| NF | nText | wstr(MAX) | Possível conversão nText → wstr |
| SerieNotaFiscal | nText | wstr(MAX) | Possível conversão nText → wstr |
| Titulo | wstr(15) | wstr(15) | Nenhuma |
| QtdTitulo | i4 | i4 | Nenhuma |
| Valor | numeric(14,4) | numeric(14,4) | Nenhuma |
| VlDesconto | numeric(14,4) | numeric(14,4) | Nenhuma |
| DtEmissao | dbTimeStamp | dbTimeStamp | Nenhuma |
| DtVencimento | dbTimeStamp | dbTimeStamp | Nenhuma |
| StatusFatura | wstr(4) | wstr(4) | Nenhuma |
