# ETL_Painel_CTE — Transformações

## Visão Geral

O package não utiliza componentes de transformação intermediários no Data Flow (não há Derived Column, Lookup, Sort, Merge, etc.). A transformação é integralmente realizada pela query SQL na origem, antes de os dados chegarem ao SSIS.

## Transformações realizadas na Query SQL (pré-processamento no servidor de origem)

### 1. Chave surrogate `cdempresa`

```sql
CONVERT(VARCHAR(50), CONCAT(a.CdEmpresa, a.NrSeqControle, a.NrDoctoFiscal)) AS cdempresa
```
- Concatena 3 campos para gerar uma chave composta textual.
- Resultado: VARCHAR(50).

### 2. Apelido emitente truncado

```sql
SUBSTRING(g.DsApelido, 1, 3) AS DsApelidoEmitente
```
- Pega apenas os 3 primeiros caracteres do apelido da empresa emitente.

### 3. Conversão de valores monetários via CONVERT(MONEY)

| Campo Origem | Campo Destino    | Conversão                 |
|--------------|------------------|---------------------------|
| a.VlFretePeso | VlFretePeso     | CONVERT(MONEY, ...)       |
| a.VlFreteValor | VlFreteValor   | CONVERT(MONEY, ...)       |
| a.VlPedagio   | VlPedagio       | CONVERT(MONEY, ...)       |
| a.VlGRIS      | VlGRIS          | CONVERT(MONEY, ...)       |
| a.VlTotalPrestacao | VlTotalPrestacao | CONVERT(MONEY, ...)  |
| a.QtPeso      | QtPeso          | CONVERT(MONEY, ...)       |
| a.QtMetrosCubicos | QtMetrosCubicos | CONVERT(MONEY, ...)   |
| a.VlMercadoria | VlMercadoria   | CONVERT(MONEY, ...)       |
| a.VlLiquido   | VlLiquido       | CONVERT(MONEY, ...)       |
| a.VlICMS      | VlICMS          | CONVERT(MONEY, ...)       |
| a.QtVolume    | QtVolume        | CONVERT(MONEY, ...)       |

> Nota: Embora a query converta para MONEY, o tipo no SSIS é `cy` (currency) na origem e `numeric p=19 s=255` no destino — discrepância documentada em issues.

### 4. Conversão de datas para VARCHAR com CONVERT(..., 103) — formato DD/MM/YYYY

| Campo Origem       | Campo Destino       |
|--------------------|---------------------|
| a.DtEmissao        | DtEmissao (wstr 30) |
| a.dtentrega        | dtentrega (wstr 30) |
| l.dtemissao        | DtEmissaoFatura (wstr 30) |
| m.dtvencimento     | dtvencimento (wstr 30) |
| xx.Dtmovimento     | DtUltimaOcorrencia (wstr 30) |
| previsao.DtPrevisaoCalculada | Previsão de Entrega (wstr 30) |

> Datas são armazenadas como strings VARCHAR no destino. Ver issues.

### 5. Concatenação de data e hora para string

```sql
CONVERT(VARCHAR, r.DtDigitacao, 103) + ' ' + CONVERT(VARCHAR, r.DtDigitacao, 108) AS [Dt Inclusao Ocorrência]
CONVERT(VARCHAR, r.DtMovimento, 103) + ' ' + CONVERT(VARCHAR, r.HrMovimento, 108) AS [Dt Ocorrencia]
```
- Produz string "DD/MM/YYYY HH:MM:SS".
- Comprimento máximo: 61 chars (definido no metadado).

### 6. CASE — Situação do CT-e

```sql
CASE WHEN ISNULL(a.inConhecimento, 0) = 0 THEN 'Normal' ELSE 'Cancelado' END AS DsSituacao
```

### 7. CASE — Tipo de Frete

```sql
CASE WHEN ISNULL(a.inTipoFrete, 0) = 0 THEN 'F' ELSE 'C' END AS DsTpFrete
```

### 8. CASE — Tipo de Emissão (15 valores mapeados)

Converte código numérico 0..15 em descrição textual. Ver query completa em origem.md.

### 9. CASE — InTDE (Local de Difícil Entrega)

```sql
CASE WHEN ISNULL(c.InLocaldificilent, 0) = 0 THEN 'Nao' ELSE 'Sim' END AS InTDE
```

### 10. CASE — Situação do Arquivo EDI

Verifica existência em `ccecceit` com 3 cenários possíveis: 'Sem Protocolo', 'Arquivado', 'Em Transito'.

### 11. Concatenação de Notas Fiscais (OUTER APPLY + FOR XML PATH)

```sql
SELECT STUFF((
    SELECT '/' + LTRIM(RTRIM(CAST(nf.NrNotaFiscal AS VARCHAR(100))))
    FROM dbo.GTCNfCon AS nf WITH (NOLOCK)
    WHERE ...
    ORDER BY nf.NrNotaFiscal
    FOR XML PATH('')
), 1, 1, '') AS NrNotaFiscalConcatenada
```
- Todas as NFs do CT-e são concatenadas separadas por `/`.
- Resultado: nText (MAX) no SSIS.

### 12. Formação do Número do Romaneio

```sql
CAST(xx.CdEmpresa AS VARCHAR(3)) + '-' + CAST(xx.CdRota AS VARCHAR(5)) + '-' + CAST(xx.CdRomaneio AS VARCHAR(6))
```
- Resultado: string de até 16 chars.

### 13. Substring do Itinerário

```sql
SUBSTRING(yy.nrmanifesto, 1, 7) AS DsItinerario
```

## Diagrama ASCII — Componentes do Data Flow

```
[ADO NET Source]
  |-- Query SQL inline (74 colunas, 31 tabelas, 10 OUTER APPLYs)
  |-- Conexão: 169.57.181.231 / SOFTRAN_TRANSLUTE
  |-- CommandTimeout: 600s
        |
        | (passthrough — sem transformações SSIS)
        v
[ADO NET Destination]
  |-- Tabela: dbo.fBaseCTE
  |-- Conexão: 10.100.86.89 / DWGrupolc
  |-- Modo: SqlBulkCopy habilitado
  |-- BatchSize: 0 (auto)
  |-- CommandTimeout: 30s
```

## Tipos de Dados — Discrepâncias Origem x Destino

| Coluna        | Tipo na Origem (SSIS) | Tipo no Destino (External) | Observação                        |
|---------------|-----------------------|----------------------------|-----------------------------------|
| VlFretePeso   | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlFreteValor  | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlPedagio     | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlGRIS        | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlTotalPrestacao | cy (money)         | numeric p=19 s=255         | Conversão implícita habilitada    |
| QtPeso        | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| QtMetrosCubicos | cy (money)          | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlMercadoria  | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlLiquido     | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlICMS        | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| QtVolume      | cy (money)            | numeric p=19 s=255         | Conversão implícita habilitada    |
| VlFreteOrigem | numeric p=14 s=4      | numeric p=14 s=4           | Consistente                       |
| VlICMSOrigem  | numeric p=14 s=4      | numeric p=14 s=4           | Consistente                       |
| DtEmissao     | wstr 30               | wstr 30                    | Data como VARCHAR — issue         |
| dtentrega     | wstr 30               | wstr 30                    | Data como VARCHAR — issue         |
| DtEmissaoFatura | wstr 30             | wstr 30                    | Data como VARCHAR — issue         |
| dtvencimento  | wstr 30               | wstr 30                    | Data como VARCHAR — issue         |
| DtUltimaOcorrencia | wstr 30          | wstr 30                    | Data como VARCHAR — issue         |
| Previsão de Entrega | wstr 30         | wstr 30                    | Data como VARCHAR — issue         |
| NrNotaFiscal  | nText                 | wstr MAX                   | Coluna LOB                        |
