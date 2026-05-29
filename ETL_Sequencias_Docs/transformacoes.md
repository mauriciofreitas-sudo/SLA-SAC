# ETL_Sequencias — Transformações

## Resumo

Este package é **passthrough puro no nível do pipeline SSIS**. Não há nenhum componente de transformação no Data Flow (sem Derived Column, Data Conversion, Lookup, Conditional Split ou Aggregate).

No entanto, a **query SQL da origem é extremamente rica** em transformações inline:
- Múltiplos `CONVERT()` para formatação de datas e valores monetários
- `CASE WHEN` para tradução de códigos em textos descritivos
- `STUFF()` + `FOR XML PATH('')` para concatenação de notas fiscais
- 8 `OUTER APPLY` para enriquecimento de dados
- 15+ tabelas juncionadas com `LEFT JOIN` e `WITH(NOLOCK)`

---

## Diagrama ASCII do Data Flow

```
[ADO NET Source]                        [ADO NET Destination]
vw_Entregas_Sequencia / query     -->   fFichasAbertas
169.57.181.231 SOFTRAN_TRANSLUTE        10.100.86.89 DWGrupolc
(37 colunas mapeadas)           ---->  (37 colunas)
```

---

## Pré-processamento SQL

**Task: Truncate fBaseSequencias**
```sql
TRUNCATE TABLE "fFichasAbertas"
```
Executado via OLEDB, ThreadHint=0.

---

## Transformações SQL Inline na Query de Origem

### CONVERT de Datas para VARCHAR (padrão 103 = dd/mm/yyyy)

```sql
CONVERT(VARCHAR, a.DtEmissao, 103) AS DtEmissao
CONVERT(VARCHAR, a.dtentrega, 103) AS dtentrega
CONVERT(VARCHAR, l.dtemissao, 103) AS DtEmissaoFatura
CONVERT(VARCHAR, m.dtvencimento, 103) AS dtvencimento
CONVERT(VARCHAR, previsao.DtPrevisaoCalculada, 103) AS [Previsão de Entrega]
CONVERT(VARCHAR, r.DtDigitacao, 103) + ' ' + CONVERT(VARCHAR, r.DtDigitacao, 108) AS [Dt Inclusao Ocorrência]
CONVERT(VARCHAR, r.DtMovimento, 103) + ' ' + CONVERT(VARCHAR, r.HrMovimento, 108) AS [Dt Ocorrencia]
```

> **Issue:** Diversas datas chegam ao destino como DT_STR (texto) em vez de dbTimeStamp — perda do tipo data.

### CONVERT de Valores Monetários (MONEY)

```sql
CONVERT(MONEY, a.VlFretePeso) AS VlFretePeso
CONVERT(MONEY, a.VlFreteValor) AS VlFreteValor
CONVERT(MONEY, a.VlPedagio) AS VlPedagio
CONVERT(MONEY, a.VlGRIS) AS VlGRIS
CONVERT(MONEY, a.VlTotalPrestacao) AS VlTotalPrestacao
CONVERT(MONEY, a.QtPeso) AS QtPeso
CONVERT(MONEY, a.QtMetrosCubicos) AS QtMetrosCubicos
CONVERT(MONEY, a.VlMercadoria) AS VlMercadoria
CONVERT(MONEY, a.VlLiquido) AS VlLiquido
CONVERT(MONEY, a.VlICMS) AS VlICMS
CONVERT(MONEY, a.QtVolume) AS QtVolume
```

### CASE WHEN — Decodificação de Tipo de Emissão (intipoemissao)

| Código | Descrição           |
|--------|---------------------|
| 0      | Normal              |
| 1      | Dev Total           |
| 2      | Reentrega           |
| 3      | Dev Parcial         |
| 4      | Complementar        |
| 5      | Cotação de Frete    |
| 6      | Armazenagem         |
| 7      | Paletizacao         |
| 8      | Diaria              |
| 9      | Redespacho Prop     |
| 10     | Agendamento         |
| 11     | Redepacho *(typo — deveria ser Redespacho)* |
| 12     | SubContrato         |
| 13     | Refaturamento       |
| 14     | Substituição        |
| 15     | Anulação            |

### OUTER APPLY — Enriquecimento de Dados

| APPLY Alias      | Fonte               | Dados Obtidos                              |
|------------------|---------------------|--------------------------------------------|
| r                | GTCMOVEN + GTCHisEn | Última ocorrência (DsUsuario, DtDigitacao, DtMovimento, DsHistoricoEntrega) |
| notas            | GTCNfCon            | NrNotaFiscal concatenadas com `/`          |
| docto            | gtcnfcon + gtcnf    | NrConheOpLog (documento origem)            |
| situacao_arq     | ccecceit            | DsSituacaoArq (Sem Protocolo/Arquivado/Em Transito) |
| ocorrencia       | gtcmoven + gtchisen | DsUltimaOcorrencia, DtUltimaOcorrencia, DsUsuario |
| romaneio         | cceroman + cceromit + gtcfundp | NomeMotorista, NrRomaneio, NrPlacaRomaneio |
| manif            | gtcmancn + gtcman + gtcitine | PlacaManifInterior, DtSaidaParceiro, DsItinerario |
| conemb           | EDIENVLG (CdTpLayout=0) | ArquivoConemb, DtEnvioConemb            |
| ocoren           | EDIENVLG (CdTpLayout=1) | ArquivoOcoren, DtEnvioOcoren            |
| doccob           | EDIENVLG (CdTpLayout=2) | ArquivoDocCob, DtEnvioDocCob            |
| pedido           | CCECOLET            | NrPedidoColeta (Pedido)                    |
| conteudo         | GTCNFCON + GTCNF + SISNATOP | ConteudoNF (NrCFOP + DsNaturezaOperacao) |

### Filtro WHERE

```sql
WHERE a.DtCancelamento IS NULL
  AND a.cdremetente IS NOT NULL
  AND a.dtemissao >= '20240101'
```

**Nota sobre `GO`:** A query no dtsx termina com `GO` — isso não é SQL válido para execução direta, mas pode ser aceito pelo ADO.NET source que pode ignorar terminadores de batch. Deve ser investigado se causa erro em runtime.

---

## Observação sobre Colunas da Query não Mapeadas

A query de origem seleciona ~60+ colunas/expressões, mas o pipeline só mapeia 37 para a tabela destino. As demais colunas da query (DsApelidoEmitente, CEPColeta, CEPDestino, dsplacaveiculo, DsMotorista, DsSituacao, DsTpFrete, DsUFOrigem, DsUFDestino, VlFreteValor, VlPedagio, VlGRIS, etc.) são calculadas mas descartadas pelo pipeline.
