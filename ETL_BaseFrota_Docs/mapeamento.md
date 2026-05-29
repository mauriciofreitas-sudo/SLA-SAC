# ETL_BaseFrota — Mapeamento de Colunas

## Data Flow: Load fBaseFrota

### Mapeamento Completo: vw_ConsultaVeiculos → fBaseFrota

| # | Coluna Origem | Coluna Destino | Tipo SSIS | Tamanho | Grupo |
|---|---|---|---|---|---|
| 1 | NrPlaca | NrPlaca | wstr | 8 | Identificacao |
| 2 | CdFabricRastreador | CdFabricRastreador | i4 | — | Rastreador |
| 3 | DsFabricante | DsFabricante | wstr | 40 | Rastreador |
| 4 | NumeroRastreador | NumeroRastreador | i4 | — | Rastreador |
| 5 | NrAnoFabricacao | NrAnoFabricacao | i4 | — | Veiculo |
| 6 | DsCores | DsCores | wstr | 40 | Veiculo |
| 7 | NrChassis | NrChassis | wstr | 25 | Veiculo |
| 8 | DtCadastro | DtCadastro | dbTimeStamp | — | Auditoria |
| 9 | DsUsuarioInc | DsUsuarioInc | wstr | 10 | Auditoria |
| 10 | DtAtualizacao | DtAtualizacao | dbTimeStamp | — | Auditoria |
| 11 | DsUsuAlteracao | DsUsuAlteracao | wstr | 10 | Auditoria |
| 12 | NrRenavan | NrRenavan | wstr | 15 | Documentacao |
| 13 | DsObservacao | DsObservacao | wstr | 500 | Complemento |
| 14 | Tipo | Tipo | wstr | 8 | Classificacao |
| 15 | Proprietario | Proprietario | wstr | 60 | Classificacao |
| 16 | DsTpVeiculo | DsTpVeiculo | wstr | 40 | Classificacao |
| 17 | CdEmpresa | CdEmpresa | i4 | — | Empresa |
| 18 | Situacao | Situacao | wstr | 7 | Status |
| 19 | VeicAnterior | VeicAnterior | wstr | 8 | Historico |
| 20 | CdANTT | CdANTT | wstr | 20 | Documentacao |

**Total de colunas mapeadas: 20**

## Distribuicao por Grupo

```
Grupo          | Qtd Colunas
---------------|------------
Identificacao  |    1
Rastreador     |    3
Veiculo        |    3
Auditoria      |    4
Documentacao   |    2
Complemento    |    1
Classificacao  |    3
Empresa        |    1
Status         |    1
Historico      |    1
---------------|------------
TOTAL          |   20
```

## Totais por Tipo de Dado SSIS

| Tipo SSIS | Qtd | Colunas |
|---|---|---|
| wstr | 13 | NrPlaca, DsFabricante, DsCores, NrChassis, DsUsuarioInc, DsUsuAlteracao, NrRenavan, DsObservacao, Tipo, Proprietario, DsTpVeiculo, Situacao, VeicAnterior, CdANTT |
| i4 | 5 | CdFabricRastreador, NumeroRastreador, NrAnoFabricacao, CdEmpresa |
| dbTimeStamp | 2 | DtCadastro, DtAtualizacao |

> Nota: wstr = Unicode string / i4 = Integer 32-bit / dbTimeStamp = datetime
