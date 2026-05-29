# Origem dos Dados — ETL_Base_Controladoria

## Identificação do Servidor de Origem

| Propriedade | Valor |
|-------------|-------|
| Servidor | 169.57.181.231 |
| Banco de Dados | SOFTRAN_TRANSLUTE |
| Usuario | softran |
| Tipo de Conexao | ADO.NET — System.Data.SqlClient |
| Connection Manager | 169.57.181.231.SOFTRAN_TRANSLUTE.softran |
| Encrypt | True |
| TrustServerCertificate | True |

---

## ADO NET Source — `VW_CONTROLADORIA_DE_FRETE`

| Propriedade | Valor |
|-------------|-------|
| Objeto de origem | `"dbo"."VW_CONTROLADORIA_DE_FRETE"` |
| Modo de acesso | AccessMode = 0 (TableOrViewName) |
| SqlCommand | *(vazio — leitura direta da view)* |
| CommandTimeout | 30 segundos |
| AllowImplicitStringConversion | true |
| validateExternalMetadata | False |

### Colunas Extraidas — `VW_CONTROLADORIA_DE_FRETE` (37 colunas)

| # | Coluna | Tipo SSIS | Tamanho/Precisao | Mapeado ao Destino | Grupo |
|---|--------|-----------|-----------------|-------------------|-------|
| 1 | ID | wstr | 1 | **Sim** | Identificacao |
| 2 | DsEmpresa | wstr | 10 | **Sim** | Empresa |
| 3 | NrFicha | wstr | 15 | **Sim** | Ficha |
| 4 | QrKmRodado | numeric | p=14, s=4 | Nao | Operacional |
| 5 | DtInclusao | wstr | 10 | Nao | Temporal |
| 6 | DtEmissao | wstr | 10 | **Sim** | Temporal |
| 7 | DtSaida | wstr | 10 | **Sim** | Temporal |
| 8 | NrPlaca | wstr | 8 | Nao | Veiculo |
| 9 | NrPlacaReboque1 | wstr | 8 | Nao | Veiculo |
| 10 | NrPlacaReboque2 | wstr | 8 | Nao | Veiculo |
| 11 | NrPlacaReboque3 | wstr | 8 | Nao | Veiculo |
| 12 | DsMotorista | wstr | 40 | Nao | Veiculo |
| 13 | DsProprietario | wstr | 60 | Nao | Veiculo |
| 14 | DsTipoFornecedor | wstr | 2 | Nao | Fornecedor |
| 15 | DsLacre | i4 | — | Nao | Operacional |
| 16 | DsFilialDestManifesto | wstr | 10 | Nao | Logistica |
| 17 | DsCidadeOrigem | wstr | 33 | **Sim** | Logistica |
| 18 | DsDestino | wstr | 33 | **Sim** | Logistica |
| 19 | DsRota | wstr | 255 | Nao | Logistica |
| 20 | CdRota | i4 | — | Nao | Logistica |
| 21 | CDTARIFA | i4 | — | Nao | Tarifa |
| 22 | TARIFA | wstr | 255 | Nao | Tarifa |
| 23 | DsCliente | wstr | 100 | **Sim** | Cliente |
| 24 | DsMaiorCliente | wstr | 100 | Nao | Cliente |
| 25 | DsTipoTransporte | wstr | 40 | **Sim** | Transporte |
| 26 | DsTipoTranspPred | wstr | 40 | Nao | Transporte |
| 27 | DsObservacaoCTRB | wstr | 1000 | Nao | CTRB |
| 28 | DsEmissorCTRB | wstr | 10 | Nao | CTRB |
| 29 | VlMercadoria | numeric | p=14, s=4 | **Sim** | Financeiro |
| 30 | NrCTRB | i4 | — | **Sim** | CTRB |
| 31 | VlFreteCTRB | numeric | p=38, s=13 | **Sim** | Financeiro |
| 32 | VlAdiantamento | numeric | p=38, s=13 | **Sim** | Financeiro |
| 33 | VlPedagioCTRB | numeric | p=38, s=13 | **Sim** | Financeiro |
| 34 | SaldoCTRB | numeric | p=38, s=4 | **Sim** | Financeiro |
| 35 | TipoVinculo | wstr | 8 | Nao | Vinculo |
| 36 | NrDoctoFiscal | wstr | 25 | Nao | Documento |
| 37 | NrRomaneio | wstr | 25 | Nao | Romaneio |
| 38 | QtPeso | numeric | p=14, s=4 | **Sim** | Carga |
| 39 | VlLiquido | numeric | p=14, s=4 | **Sim** | Financeiro |
| 40 | PesoTotalFicha | numeric | p=38, s=4 | Nao | Carga |
| 41 | VlCustoTransferencia | numeric | p=38, s=13 | Nao | Financeiro |
| 42 | VlCustoEntrega | numeric | p=38, s=13 | Nao | Financeiro |
| 43 | DsTipoVeiculo | wstr | 8 | Nao | Veiculo |
| 44 | DtCTRB | wstr | 10 | Nao | Temporal |
| 45 | Qtde | i4 | — | Nao | Operacional |
| 46 | QTVISITA | i4 | — | Nao | Operacional |
| 47 | VlComissaoFV | numeric | p=38, s=11 | Nao | Financeiro |
| 48 | VlAcrescimosFV | numeric | p=38, s=13 | Nao | Financeiro |
| 49 | VlDescontosFV | numeric | p=38, s=13 | Nao | Financeiro |
| 50 | VlIRRF | numeric | p=38, s=13 | Nao | Financeiro |
| 51 | VlSESTSENAT | numeric | p=38, s=13 | Nao | Financeiro |
| 52 | VlINSS | numeric | p=38, s=13 | Nao | Financeiro |
| 53 | AcrescimosCTRB | numeric | p=38, s=4 | Nao | Financeiro |
| 54 | DescontosCTRB | numeric | p=38, s=4 | Nao | Financeiro |
| 55 | ContratoRepom | wstr | 30 | Nao | Contrato |

> A view retorna 55 colunas na source, mas apenas 17 sao mapeadas para o destino.

### Configuracoes de Erro da Source

| Coluna | errorRowDisposition | truncationRowDisposition |
|--------|---------------------|--------------------------|
| ID | IgnoreFailure | FailComponent |
| DsEmpresa, NrFicha, DtEmissao, DtSaida... | IgnoreFailure | IgnoreFailure |
| DsMaiorCliente | **FailComponent** | IgnoreFailure |

> Atencao: apenas a coluna `DsMaiorCliente` tem `errorRowDisposition=FailComponent`. Todas as outras usam `IgnoreFailure`, o que significa que erros de conversao sao silenciosamente ignorados.
