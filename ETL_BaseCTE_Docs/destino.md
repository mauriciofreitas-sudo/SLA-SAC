# Destino dos Dados — ETL_BaseCTE

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `10.100.86.89` |
| **Banco de dados** | `DWGrupolc` |
| **Tabela de destino** | `dbo.fBaseCTE` |
| **Usuário de conexão** | `sqldba` |
| **Tipo de conexão (Data Flow)** | ADO.NET (`System.Data.SqlClient`) |
| **Tipo de conexão (Delete)** | OLE DB (SQLNCLI11.1) |
| **Modo de inserção** | Bulk Insert (`UseBulkInsertWhenPossible = true`) |
| **Timeout de comando** | 600 segundos |
| **Tratamento de erros** | `FailComponent` — falha interrompe a task |

---

## Diagrama do Destino

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  10.100.86.89                                     │
│  BANCO:     DWGrupolc                                        │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  TABELA: dbo.fBaseCTE                                  │  │
│  │                                                        │  │
│  │  Tabela fato principal do DW de transportes            │  │
│  │  Um registro por CT-e emitido                          │  │
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
dbo.fBaseCTE                     dbo.fBaseCTE
                                 (todas as colunas da view)
[Remove TODOS os registros]      [Insere TODOS os registros]
```

| Aspecto | Comportamento |
|---|---|
| Registros existentes | Removidos antes da carga |
| Registros novos na fonte | Inseridos na carga |
| Registros atualizados na fonte | Refletidos após a carga |
| Registros deletados na fonte | Não aparecem após a carga |
| Janela de indisponibilidade | Tabela vazia entre DELETE e fim do INSERT |

---

## Schema da Tabela Destino

### Grupo 1 — Identificação do CT-e

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `EmpresaCTE` | int | ? |
| `NrDoctoFiscal` | int | ? |
| `TipoDoctoFiscal` | nvarchar(10) | ? |
| `DtEmissao` | datetime | ? |
| `AnoMes` | int | ? |
| `Ano` | int | ? |
| `Mes` | int | ? |

### Grupo 2 — Filial e Localização

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `Filial` | nvarchar(10) | ? |
| `DsFilial` | nvarchar(100) | ? |
| `UFOrigem` | nvarchar(2) | ? |
| `CidadeOrigem` | nvarchar(50) | ? |
| `UFDestino` | nvarchar(2) | ? |
| `CidadeDestino` | nvarchar(50) | ? |

### Grupo 3 — Partes Envolvidas

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdRemetente` | nvarchar(14) | ? |
| `DsRemetente` | nvarchar(100) | ? |
| `CdDestinatario` | nvarchar(14) | ? |
| `DsDestinatario` | nvarchar(100) | ? |
| `CdPagador` | nvarchar(14) | ? |
| `DsPagador` | nvarchar(100) | ? |
| `CdGrupoCliente` | int | ? |
| `DsGrupoCliente` | nvarchar(100) | ? |

### Grupo 4 — Motorista e Veículo

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdMotorista` | nvarchar(14) | ? |
| `DsMotorista` | nvarchar(100) | ? |
| `VinculoMotorista` | nvarchar(20) | ? |
| `Veiculo` | nvarchar(8) | ? |
| `Carreta` | nvarchar(8) | ? |

### Grupo 5 — Valores Financeiros

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlReceita` | numeric(14,4) | ? |
| `VlFrete` | numeric(14,4) | ? |
| `VlMargem` | numeric(14,4) | ? |
| `PctMargem` | numeric(5,2) | ? |
| `VlFretePeso` | numeric(14,4) | ? |
| `VlFreteValor` | numeric(14,4) | ? |
| `VlPedagio` | numeric(14,4) | ? |
| `VlGRIS` | numeric(14,4) | ? |
| `VlTotalPrestacao` | numeric(14,4) | ? |

### Grupo 6 — Ficha e CTRB

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `NrFicha` | int | ? |
| `NrCTRB` | int | ? |
| `DtEmissaoCTRB` | datetime | ? |

---

## Configurações de Performance

| Configuração | Valor | Observação |
|---|---|---|
| `UseBulkInsertWhenPossible` | `true` | SqlBulkCopy — inserção em massa |
| `BatchSize` | `0` | Usa buffer interno do SSIS |
| `CommandTimeout` | `600s` | Adequado para volumes históricos (2024–2026) |
