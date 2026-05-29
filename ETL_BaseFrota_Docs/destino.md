# ETL_BaseFrota — Destino de Dados

## Servidor de Destino

| Campo | Valor |
|---|---|
| Servidor | 10.100.86.89 |
| Banco | DWGrupolc |
| Usuario | sqldba |
| Connection Manager | 10.100.86.89.DWGrupolc.sqldba (ADO.NET SqlClient) |

## Data Flow: Load fBaseFrota

### Componente Destination

| Campo | Valor |
|---|---|
| Nome | ADO NET Destination |
| Tipo | ADO.NET Destination (Microsoft.ADONETDestination) |
| Tabela de Destino | `"fBaseFrota"` |
| BatchSize | 0 (usa o tamanho do buffer interno do SSIS) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true (usa SqlBulkCopy quando possivel) |
| validateExternalMetadata | False |

## Estrategia de Carga

**Modo: Full Truncate + Reload**

1. Task `Truncate fBaseFrota` executa `TRUNCATE TABLE "fBaseFrota"` no DWGrupolc
2. Task `Load fBaseFrota` carrega todos os dados da view de origem

Nao ha carga incremental — cada execucao substitui toda a tabela.

## Schema de Destino — Tabela fBaseFrota

| # | Coluna | Tipo SSIS | Tamanho | Coluna de Destino |
|---|---|---|---|---|
| 1 | NrPlaca | wstr | 8 | NrPlaca |
| 2 | CdFabricRastreador | i4 | — | CdFabricRastreador |
| 3 | DsFabricante | wstr | 40 | DsFabricante |
| 4 | NumeroRastreador | i4 | — | NumeroRastreador |
| 5 | NrAnoFabricacao | i4 | — | NrAnoFabricacao |
| 6 | DsCores | wstr | 40 | DsCores |
| 7 | NrChassis | wstr | 25 | NrChassis |
| 8 | DtCadastro | dbTimeStamp | — | DtCadastro |
| 9 | DsUsuarioInc | wstr | 10 | DsUsuarioInc |
| 10 | DtAtualizacao | dbTimeStamp | — | DtAtualizacao |
| 11 | DsUsuAlteracao | wstr | 10 | DsUsuAlteracao |
| 12 | NrRenavan | wstr | 15 | NrRenavan |
| 13 | DsObservacao | wstr | 500 | DsObservacao |
| 14 | Tipo | wstr | 8 | Tipo |
| 15 | Proprietario | wstr | 60 | Proprietario |
| 16 | DsTpVeiculo | wstr | 40 | DsTpVeiculo |
| 17 | CdEmpresa | i4 | — | CdEmpresa |
| 18 | Situacao | wstr | 7 | Situacao |
| 19 | VeicAnterior | wstr | 8 | VeicAnterior |
| 20 | CdANTT | wstr | 20 | CdANTT |

## Configuracoes do Destino

| Configuracao | Valor |
|---|---|
| Modo de Acesso | 0 (OpenRowset — nome da tabela diretamente) |
| Tratamento de erro nas linhas | FailComponent |
| hasSideEffects | true |
| Error Output | ADO NET Destination Error Output (desconectado) |

## Pre-processamento

```sql
TRUNCATE TABLE "fBaseFrota"
```
Executado pela task `Truncate fBaseFrota` (OLEDB, conexao 10.100.86.89.DWGrupolc.sqldba1) antes do Data Flow.
