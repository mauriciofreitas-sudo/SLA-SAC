# Transformações — ETL_Base_Controladoria

## Resumo

Este package e um **passthrough seletivo**: nao ha transformacoes expliciticas (Derived Column, Lookup, etc.), mas a carga e seletiva — a source retorna 55 colunas e apenas 17 sao mapeadas para o destino. As 38 colunas restantes sao ignoradas.

---

## Componentes do Data Flow — Data Flow Task

```
+----------------------------+          +-----------------------------+
|  Origem do ADO NET         |          |  Destino do ADO NET         |
|  VW_CONTROLADORIA_DE_FRETE | -------> | VW_CONTROLADORIA_BASE_EMISSAO|
|  (55 colunas na source)    |          | (17 colunas mapeadas)       |
|                            |          |  errorRowDisposition=       |
+----------------------------+          |  IgnoreFailure              |
         |                              +-----------------------------+
         v (Error Output — nao conectado)
```

---

## Pré-processamento (SQL Tasks antes do Data Flow)

| Ordem | Task | SQL Executado | ThreadHint | Conexao |
|-------|------|---------------|------------|---------|
| 1 | Limpar Tabela fControladoria | `DELETE FROM [DBStage].[dbo].[fControladoria]` | 0 | 10.100.86.89 1 (sem banco especificado) |

---

## Colunas Ignoradas na Carga (38 de 55 nao mapeadas)

| # | Coluna | Tipo SSIS | Grupo | Motivo provavel |
|---|--------|-----------|-------|-----------------|
| 1 | QrKmRodado | numeric(14,4) | Operacional | Nao necessario para controladoria de emissao |
| 2 | DtInclusao | wstr(10) | Temporal | Nao usado no relatorio |
| 3 | NrPlaca | wstr(8) | Veiculo | Detalhes do veiculo fora do escopo |
| 4 | NrPlacaReboque1 | wstr(8) | Veiculo | Idem |
| 5 | NrPlacaReboque2 | wstr(8) | Veiculo | Idem |
| 6 | NrPlacaReboque3 | wstr(8) | Veiculo | Idem |
| 7 | DsMotorista | wstr(40) | Veiculo | Idem |
| 8 | DsProprietario | wstr(60) | Veiculo | Idem |
| 9 | DsTipoFornecedor | wstr(2) | Fornecedor | Fora do escopo |
| 10 | DsLacre | i4 | Operacional | Fora do escopo |
| 11 | DsFilialDestManifesto | wstr(10) | Logistica | Nao usado |
| 12 | DsRota | wstr(255) | Logistica | Nao usado |
| 13 | CdRota | i4 | Logistica | Nao usado |
| 14 | CDTARIFA | i4 | Tarifa | Nao usado |
| 15 | TARIFA | wstr(255) | Tarifa | Nao usado |
| 16 | DsMaiorCliente | wstr(100) | Cliente | Nao necessario (so DsCliente e mapeado) |
| 17 | DsTipoTranspPred | wstr(40) | Transporte | Nao necessario |
| 18 | DsObservacaoCTRB | wstr(1000) | CTRB | Nao necessario |
| 19 | DsEmissorCTRB | wstr(10) | CTRB | Nao necessario |
| 20 | TipoVinculo | wstr(8) | Vinculo | Nao necessario |
| 21 | NrDoctoFiscal | wstr(25) | Documento | Nao necessario |
| 22 | NrRomaneio | wstr(25) | Romaneio | Nao necessario |
| 23 | PesoTotalFicha | numeric(38,4) | Carga | Apenas QtPeso e mapeado |
| 24 | VlCustoTransferencia | numeric(38,13) | Financeiro | Nao necessario |
| 25 | VlCustoEntrega | numeric(38,13) | Financeiro | Nao necessario |
| 26 | DsTipoVeiculo | wstr(8) | Veiculo | Nao necessario |
| 27 | DtCTRB | wstr(10) | Temporal | Nao necessario |
| 28 | Qtde | i4 | Operacional | Nao necessario |
| 29 | QTVISITA | i4 | Operacional | Nao necessario |
| 30 | VlComissaoFV | numeric(38,11) | Financeiro | Nao necessario |
| 31 | VlAcrescimosFV | numeric(38,13) | Financeiro | Nao necessario |
| 32 | VlDescontosFV | numeric(38,13) | Financeiro | Nao necessario |
| 33 | VlIRRF | numeric(38,13) | Financeiro | Nao necessario |
| 34 | VlSESTSENAT | numeric(38,13) | Financeiro | Nao necessario |
| 35 | VlINSS | numeric(38,13) | Financeiro | Nao necessario |
| 36 | AcrescimosCTRB | numeric(38,4) | Financeiro | Nao necessario |
| 37 | DescontosCTRB | numeric(38,4) | Financeiro | Nao necessario |
| 38 | ContratoRepom | wstr(30) | Contrato | Nao necessario |

---

## Mapeamento de Tipos de Dados — Colunas Mapeadas

| Coluna | Tipo Source | Tipo Destino | Observacao |
|--------|-------------|--------------|------------|
| ID | wstr(1) | wstr(1) | Passthrough |
| DsEmpresa | wstr(10) | wstr(10) | Passthrough |
| NrFicha | wstr(15) | wstr(15) | Passthrough |
| DtEmissao | **wstr(10)** | **wstr(10)** | **CRITICO: Data como string "YYYY-MM-DD"** |
| DtSaida | **wstr(10)** | **wstr(10)** | **CRITICO: Data como string "YYYY-MM-DD"** |
| DsCidadeOrigem | wstr(33) | wstr(33) | Passthrough |
| DsDestino | wstr(33) | wstr(33) | Passthrough |
| DsCliente | wstr(100) | wstr(100) | Passthrough |
| DsTipoTransporte | wstr(40) | wstr(40) | Passthrough |
| VlMercadoria | numeric(14,4) | numeric(14,4) | Passthrough |
| NrCTRB | i4 | i4 | Passthrough |
| VlFreteCTRB | numeric(38,13) | numeric(38,13) | Precisao muito alta (38,13) |
| VlAdiantamento | numeric(38,13) | numeric(38,13) | Precisao muito alta |
| VlPedagioCTRB | numeric(38,13) | numeric(38,13) | Precisao muito alta |
| SaldoCTRB | numeric(38,4) | numeric(38,4) | Precisao muito alta |
| QtPeso | numeric(14,4) | numeric(14,4) | Passthrough |
| VlLiquido | numeric(14,4) | numeric(14,4) | Passthrough |
