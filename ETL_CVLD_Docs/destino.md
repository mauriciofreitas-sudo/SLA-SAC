# Destino dos Dados — ETL_CVLD

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `10.100.86.89` |
| **Banco de dados** | `DWGrupolc` |
| **Tabela de destino** | `dbo.fCVLD` |
| **Usuário de conexão** | `sqldba` |
| **Tipo de conexão (Data Flow)** | ADO.NET (`System.Data.SqlClient`) |
| **Tipo de conexão (Delete)** | OLE DB (SQLNCLI11.1) |
| **Modo de inserção** | Bulk Insert (`UseBulkInsertWhenPossible = true`) |
| **Timeout de comando** | 600 segundos |
| **Tratamento de erros** | `FailComponent` |

---

## Diagrama do Destino

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  10.100.86.89                                     │
│  BANCO:     DWGrupolc                                        │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  TABELA: dbo.fCVLD                                     │  │
│  │                                                        │  │
│  │  Tabela fato de custo CTRB — terceiros e motoristas    │  │
│  │  Um registro por CTRB emitido                          │  │
│  │                                                        │  │
│  │  Estratégia: FULL REFRESH                              │  │
│  │  DELETE todos + INSERT todos                           │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Estratégia de Carga

```
PASSO 1                          PASSO 2
──────────────────────           ────────────────────────────────
Execute SQL Task                 Data Flow Task

DELETE FROM                      INSERT bulk
dbo.fCVLD                        dbo.fCVLD
                                 (todas as colunas da view)
[Remove TODOS os registros]      [Insere TODOS os registros]
```

| Aspecto | Comportamento |
|---|---|
| Registros existentes | Removidos antes da carga |
| CTRBs novos na fonte | Inseridos na carga |
| CTRBs pagos/cancelados | Status refletido após recarga |
| Janela de indisponibilidade | Tabela vazia entre DELETE e fim do INSERT |

---

## Schema da Tabela Destino — dbo.fCVLD

### Grupo 1 — Identificação do CTRB

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `NumeroCTRB` | int | ? |
| `EmpresaCTRB` | int | ? |
| `DtEmissaoCTRB` | datetime | ? |
| `DtPagamentoCTRB` | datetime | ✓ |
| `StatusCTRB` | nvarchar(20) | ? |
| `AnoMes` | int | ? |

### Grupo 2 — Filial e Localização

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `Filial` | nvarchar(10) | ? |
| `UFOrigem` | nvarchar(2) | ✓ |
| `UFDestino` | nvarchar(2) | ✓ |

### Grupo 3 — Transportadora / Motorista

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdTransportadora` | nvarchar(14) | ? |
| `DsTransportadora` | nvarchar(100) | ? |
| `CdMotorista` | nvarchar(14) | ✓ |
| `DsMotorista` | nvarchar(100) | ✓ |
| `TipoVinculo` | nvarchar(20) | ? |
| `Veiculo` | nvarchar(8) | ✓ |
| `Carreta` | nvarchar(8) | ✓ |

### Grupo 4 — Ficha de Viagem

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `NrFicha` | int | ✓ |
| `DtEmissaoFicha` | datetime | ✓ |

### Grupo 5 — Valores do CTRB

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlFreteCTRB` | numeric(14,4) | ? |
| `VlSaldoCTRB` | numeric(14,4) | ✓ |
| `VlAdiantamentoCTRB` | numeric(14,4) | ✓ |
| `VlPedagioCTRB` | numeric(14,4) | ✓ |

### Grupo 6 — Encargos e Aluguel

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlINSS` | numeric(14,4) | ✓ |
| `VlSESTSENAT` | numeric(14,4) | ✓ |
| `VlIRRF` | numeric(14,4) | ✓ |
| `VlAluguelCarreta` | numeric(14,4) | ✓ |
| `FlAluguel` | bit | ? |

---

## Configurações de Performance

| Configuração | Valor | Observação |
|---|---|---|
| `UseBulkInsertWhenPossible` | `true` | SqlBulkCopy |
| `BatchSize` | `0` | Buffer interno SSIS |
| `CommandTimeout` | `600s` | Para volumes históricos amplos |
