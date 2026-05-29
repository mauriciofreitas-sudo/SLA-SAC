# Destino dos Dados — ETL_ControladoriaFrete

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `10.100.86.89` |
| **Banco de dados** | `DWGrupolc` |
| **Tabela de destino** | `dbo.fControladoriaFrete` |
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
│  │  TABELA: dbo.fControladoriaFrete                       │  │
│  │                                                        │  │
│  │  Resultado financeiro por operação (CT-e × CTRB)       │  │
│  │  Um registro por CT-e vinculado a ficha de viagem      │  │
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
PASSO 1                             PASSO 2
──────────────────────              ────────────────────────────────
Execute SQL Task                    Data Flow Task

DELETE FROM                         INSERT bulk
dbo.fControladoriaFrete             dbo.fControladoriaFrete
                                    (todas as colunas da view)
[Remove TODOS os registros]         [Insere TODOS os registros]
```

| Aspecto | Comportamento |
|---|---|
| Registros existentes | Removidos antes da carga |
| Operações novas na fonte | Inseridas na carga |
| Ajustes retroativos de custo | Refletidos após a recarga |
| Janela de indisponibilidade | Tabela vazia entre DELETE e fim do INSERT |

> **Atenção**: este ETL deve ser agendado **após** `ETL_BaseCTE`, `ETL_Fichas` e `ETL_CVLD` para garantir que os dados de origem já estejam consistentes no SOFTRAN.

---

## Schema da Tabela Destino — dbo.fControladoriaFrete

### Grupo 1 — Identificação da Operação

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `EmpresaCTE` | int | ? |
| `NrDoctoFiscal` | int | ? |
| `TipoDoctoFiscal` | nvarchar(10) | ? |
| `DtEmissao` | datetime | ? |
| `AnoMes` | int | ? |
| `NrFicha` | int | ✓ |
| `NrCTRB` | int | ✓ |

### Grupo 2 — Filial e Rota

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `Filial` | nvarchar(10) | ? |
| `UFOrigem` | nvarchar(2) | ✓ |
| `CidadeOrigem` | nvarchar(50) | ✓ |
| `UFDestino` | nvarchar(2) | ✓ |
| `CidadeDestino` | nvarchar(50) | ✓ |
| `CdRota` | int | ✓ |

### Grupo 3 — Cliente

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdRemetente` | nvarchar(14) | ✓ |
| `DsRemetente` | nvarchar(100) | ✓ |
| `CdDestinatario` | nvarchar(14) | ✓ |
| `DsDestinatario` | nvarchar(100) | ✓ |
| `CdPagador` | nvarchar(14) | ✓ |
| `DsPagador` | nvarchar(100) | ✓ |
| `CdGrupoCliente` | int | ✓ |
| `DsGrupoCliente` | nvarchar(100) | ✓ |

### Grupo 4 — Motorista e Transportadora

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdMotorista` | nvarchar(14) | ✓ |
| `DsMotorista` | nvarchar(100) | ✓ |
| `CdTransportadora` | nvarchar(14) | ✓ |
| `DsTransportadora` | nvarchar(100) | ✓ |
| `VinculoMotorista` | nvarchar(20) | ✓ |
| `Veiculo` | nvarchar(8) | ✓ |
| `Carreta` | nvarchar(8) | ✓ |

### Grupo 5 — Receita (CT-e)

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlReceita` | numeric(14,4) | ? |
| `VlFretePeso` | numeric(14,4) | ✓ |
| `VlFreteValor` | numeric(14,4) | ✓ |
| `VlPedagioReceita` | numeric(14,4) | ✓ |
| `VlGRIS` | numeric(14,4) | ✓ |

### Grupo 6 — Custo (CTRB)

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlCustoCTRB` | numeric(14,4) | ✓ |
| `VlFreteCTRB` | numeric(14,4) | ✓ |
| `VlPedagioCTRB` | numeric(14,4) | ✓ |
| `VlAdiantamentoCTRB` | numeric(14,4) | ✓ |
| `VlSaldoCTRB` | numeric(14,4) | ✓ |
| `VlEncargos` | numeric(14,4) | ✓ |
| `VlAluguelCarreta` | numeric(14,4) | ✓ |

### Grupo 7 — Resultado

| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlMargem` | numeric(14,4) | ? |
| `PctMargem` | numeric(5,2) | ? |

---

## Configurações de Performance

| Configuração | Valor | Observação |
|---|---|---|
| `UseBulkInsertWhenPossible` | `true` | SqlBulkCopy |
| `BatchSize` | `0` | Buffer interno SSIS |
| `CommandTimeout` | `600s` | A view envolve JOINs pesados entre 3 entidades |
