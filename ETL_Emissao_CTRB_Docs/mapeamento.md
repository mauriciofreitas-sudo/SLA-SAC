# ETL_Emissao_CTRB — Mapeamento de Colunas

## Data Flow: Tarefa Fluxo de Dados (+ Data Flow ativo raiz)
### Mapeamento Completo: vw_Base_Emissão_CTRB → f_base_emissao_ctrb

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Grupo |
|---|---|---|---|---|
| 1 | DsEmpresa | str/wstr | 10 | Empresa |
| 2 | NrFicha | str/wstr | 15 | Identificacao |
| 3 | QrKmRodado | numeric | p14,s4 | Operacional |
| 4 | DtInclusao | str/wstr | 30 | Data (string) |
| 5 | DtEmissao | dbTimeStamp | — | Data |
| 6 | DtSaida | str/wstr | 30 | Data (string) |
| 7 | NrPlaca | str/wstr | 8 | Veiculo |
| 8 | NrPlacaReboque1 | str/wstr | 8 | Veiculo |
| 9 | NrPlacaReboque2 | str/wstr | 8 | Veiculo |
| 10 | NrPlacaReboque3 | str/wstr | 8 | Veiculo |
| 11 | DsMotorista | str/wstr | 40 | Motorista |
| 12 | DsProprietario | str/wstr | 60 | Proprietario |
| 13 | DsTipoFornecedor | str/wstr | 2 | Classificacao |
| 14 | DsLacre | i4 | — | Operacional |
| 15 | DsFilialDestManifesto | str/wstr | 10 | Logistica |
| 16 | DsCidadeOrigem | str/wstr | 33 | Localizacao |
| 17 | DsDestino | str/wstr | 33 | Localizacao |
| 18 | DsRota | str/wstr | 255 | Logistica |
| 19 | CdRota | i4 | — | Logistica |
| 20 | CDTARIFA | i4 | — | Financeiro |
| 21 | TARIFA | str/wstr | 255 | Financeiro |
| 22 | DsCliente | str/wstr | 100 | Cliente |
| 23 | DsMaiorCliente | str/wstr | 100 | Cliente |
| 24 | DsTipoTransporte | str/wstr | 40 | Transporte |
| 25 | DsTipoTranspPred | str/wstr | 40 | Transporte |
| 26 | DsObservacaoCTRB | str/wstr | 1000 | Complemento |
| 27 | DsEmissorCTRB | str/wstr | 10 | Auditoria |
| 28 | VlMercadoria | numeric | p14,s4 | Valor |
| 29 | NrCTRB | i4 | — | Identificacao |
| 30 | ValorTotalCTRB | numeric | p38,s4 | Valor |
| 31 | VlFreteCTRB | numeric | p38,s13/s4 | Valor |
| 32 | VlAdiantamento | numeric | p38,s13/s4 | Valor |
| 33 | VlPedagioCTRB | numeric | p38,s13/s4 | Valor |
| 34 | SaldoCTRB | numeric | p38,s4 | Valor |
| 35 | TipoVinculo | str/wstr | 8 | Classificacao |
| 36 | NrDoctoFiscal | str/wstr | 25 | Documento |
| 37 | NrRomaneio | str/wstr | 25 | Logistica |
| 38 | QtPeso | numeric | p14,s4 | Operacional |
| 39 | VlLiquido | numeric | p14,s4 | Valor |
| 40 | PesoTotalFicha | numeric | p38,s4 | Operacional |
| 41 | VlCustoTransferencia | numeric | p38,s13/s6 | Valor |
| 42 | VlCustoEntrega | numeric | p38,s13/s4 | Valor |
| 43 | DsTipoVeiculo | str/wstr | 8 | Veiculo |
| 44 | DtCTRB | str/wstr | 30 | Data (string) |
| 45 | Qtde | i4 | — | Operacional |
| 46 | QTVISITA | i4 | — | Operacional |
| 47 | VlComissaoFV | numeric | p14,s4 | Valor |
| 48 | VlAcrescimosFV | numeric | p38,s13 | Valor |
| 49 | VlDescontosFV | numeric | p38,s13 | Valor |
| 50 | VlIRRF | numeric | p38,s4 | Valor |
| 51 | VlSESTSENAT | numeric | p38,s4 | Valor |
| 52 | VlINSS | numeric | p38,s4 | Valor |
| 53 | AcrescimosCTRB | numeric | p38,s4 | Valor |
| 54 | DescontosCTRB | numeric | p38,s4 | Valor |
| 55 | ContratoRepom | str/wstr | 30 | Contrato |

**Total de colunas mapeadas: 55**

## Distribuicao por Grupo

```
Grupo          | Qtd Colunas
---------------|------------
Valor          |   17
Veiculo        |    5
Logistica      |    4
Localizacao    |    2
Operacional    |    5
Identificacao  |    2
Data           |    1
Data (string)  |    3
Cliente        |    2
Transporte     |    2
Classificacao  |    3
Empresa        |    1
Motorista      |    1
Proprietario   |    1
Financeiro     |    2
Complemento    |    1
Auditoria      |    1
Documento      |    1
Contrato       |    1
---------------|------------
TOTAL          |   55
```

## Totais por Tipo de Dado SSIS

| Tipo | Qtd |
|---|---|
| str/wstr | 28 |
| numeric | 22 |
| i4 | 4 |
| dbTimeStamp | 1 |
