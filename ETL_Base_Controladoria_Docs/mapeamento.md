# Mapeamento de Colunas — ETL_Base_Controladoria

## Visao Geral

| Data Flow | Source | Destination | Col. Source | Col. Mapeadas | Col. Ignoradas | Transformacoes |
|-----------|--------|-------------|-------------|---------------|----------------|----------------|
| Data Flow Task | VW_CONTROLADORIA_DE_FRETE | VW_CONTROLADORIA_BASE_EMISSAO | 55 | 17 | 38 | Nenhuma |

---

## Mapeamento Completo — 17 Colunas Mapeadas

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Origem | Grupo | Observacao |
|---|--------|-----------|-----------------|--------|-------|------------|
| 1 | ID | wstr | 1 | VW_CONTROLADORIA_DE_FRETE.ID | Identificacao | Campo de 1 char — identificador de tipo? |
| 2 | DsEmpresa | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DsEmpresa | Empresa | |
| 3 | NrFicha | wstr | 15 | VW_CONTROLADORIA_DE_FRETE.NrFicha | Ficha | |
| 4 | DtEmissao | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DtEmissao | Temporal | DATA COMO STRING — issue critico |
| 5 | DtSaida | wstr | 10 | VW_CONTROLADORIA_DE_FRETE.DtSaida | Temporal | DATA COMO STRING — issue critico |
| 6 | DsCidadeOrigem | wstr | 33 | VW_CONTROLADORIA_DE_FRETE.DsCidadeOrigem | Logistica | |
| 7 | DsDestino | wstr | 33 | VW_CONTROLADORIA_DE_FRETE.DsDestino | Logistica | |
| 8 | DsCliente | wstr | 100 | VW_CONTROLADORIA_DE_FRETE.DsCliente | Cliente | |
| 9 | DsTipoTransporte | wstr | 40 | VW_CONTROLADORIA_DE_FRETE.DsTipoTransporte | Transporte | |
| 10 | VlMercadoria | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.VlMercadoria | Financeiro | |
| 11 | NrCTRB | i4 | — | VW_CONTROLADORIA_DE_FRETE.NrCTRB | CTRB | |
| 12 | VlFreteCTRB | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlFreteCTRB | Financeiro | Precisao excessiva (38,13) |
| 13 | VlAdiantamento | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlAdiantamento | Financeiro | Precisao excessiva |
| 14 | VlPedagioCTRB | numeric | p=38, s=13 | VW_CONTROLADORIA_DE_FRETE.VlPedagioCTRB | Financeiro | Precisao excessiva |
| 15 | SaldoCTRB | numeric | p=38, s=4 | VW_CONTROLADORIA_DE_FRETE.SaldoCTRB | Financeiro | Precisao excessiva |
| 16 | QtPeso | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.QtPeso | Carga | |
| 17 | VlLiquido | numeric | p=14, s=4 | VW_CONTROLADORIA_DE_FRETE.VlLiquido | Financeiro | |

---

## Distribuicao por Grupo — 17 Colunas Mapeadas

```
Financeiro  [##########] 6 colunas (35%)
Temporal    [####]        2 colunas (12%) — DATAS COMO STRING
Logistica   [####]        2 colunas (12%)
Identificac [##]          1 coluna  (6%)
Empresa     [##]          1 coluna  (6%)
Ficha       [##]          1 coluna  (6%)
Cliente     [##]          1 coluna  (6%)
Transporte  [##]          1 coluna  (6%)
CTRB        [##]          1 coluna  (6%)
Carga       [##]          1 coluna  (6%)
                           Total: 17 colunas
```

---

## Colunas Presentes na Source mas Ignoradas (38 colunas)

| Grupo | Colunas Ignoradas |
|-------|-------------------|
| Veiculo (8) | NrPlaca, NrPlacaReboque1, NrPlacaReboque2, NrPlacaReboque3, DsMotorista, DsProprietario, DsTipoVeiculo, DsTipoFornecedor |
| Financeiro (14) | VlCustoTransferencia, VlCustoEntrega, VlComissaoFV, VlAcrescimosFV, VlDescontosFV, VlIRRF, VlSESTSENAT, VlINSS, AcrescimosCTRB, DescontosCTRB, PesoTotalFicha (carga) |
| Logistica (4) | DsRota, CdRota, DsFilialDestManifesto, DsLacre |
| Tarifa (2) | CDTARIFA, TARIFA |
| Temporal (3) | DtInclusao, DtCTRB, QrKmRodado |
| CTRB (2) | DsObservacaoCTRB, DsEmissorCTRB |
| Outros (7) | DsMaiorCliente, DsTipoTranspPred, TipoVinculo, NrDoctoFiscal, NrRomaneio, Qtde, QTVISITA, ContratoRepom |

---

## Tipos de Dados — Resumo (Colunas Mapeadas)

| Tipo SSIS | Equivalente SQL Server | Quantidade | Observacao |
|-----------|----------------------|------------|------------|
| wstr | NVARCHAR | 9 colunas | Incluindo DtEmissao e DtSaida (datas como string!) |
| numeric(14,4) | DECIMAL(14,4) | 3 colunas | VlMercadoria, QtPeso, VlLiquido |
| numeric(38,13) | DECIMAL(38,13) | 3 colunas | Precisao excessiva: VlFreteCTRB, VlAdiantamento, VlPedagioCTRB |
| numeric(38,4) | DECIMAL(38,4) | 1 coluna | SaldoCTRB |
| i4 | INT | 1 coluna | NrCTRB |
