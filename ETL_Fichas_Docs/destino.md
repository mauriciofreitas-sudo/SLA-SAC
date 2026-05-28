# Destino dos Dados — ETL_Fichas

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `10.100.86.89` |
| **Banco de dados** | `DWGrupolc` |
| **Tabela de destino** | `dbo.fFichaViagem` |
| **Usuário de conexão** | `sqldba` |
| **Tipo de conexão (Data Flow)** | ADO.NET (`System.Data.SqlClient`) |
| **Tipo de conexão (Delete)** | OLE DB (SQLNCLI11.1) |
| **Modo de inserção** | Bulk Insert (`UseBulkInsertWhenPossible = true`) |
| **Tamanho do batch** | 0 (usa buffer interno do SSIS) |
| **Timeout de comando** | 30 segundos |
| **Tratamento de erros** | `FailComponent` — falha interrompe a task |

---

## Diagrama do Destino

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  10.100.86.89                                     │
│  BANCO:     DWGrupolc                                        │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  TABELA: dbo.fFichaViagem                              │  │
│  │                                                        │  │
│  │  91 colunas no schema total                            │  │
│  │  ├── 51 colunas ← carregadas por este ETL              │  │
│  │  └── 40 colunas ← NULL / outros processos             │  │
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
dbo.fFichaViagem                 dbo.fFichaViagem
                                 (51 colunas da view de origem)
[Remove TODOS os registros]      [Insere TODOS os registros da view]
```

A estratégia é **full-refresh**: apaga tudo e recarrega do zero. Isso garante consistência total com a fonte, mas tem implicações:

| Aspecto | Comportamento |
|---|---|
| Registros existentes | Removidos antes da carga |
| Registros novos | Inseridos na carga |
| Registros atualizados na fonte | Refletidos após a carga |
| Registros deletados na fonte | Não aparecem após a carga |
| Janela de indisponibilidade | Tabela vazia entre o DELETE e o fim do INSERT |

---

## Schema da Tabela Destino (91 colunas)

### Colunas carregadas por este ETL (51)

#### Ficha de Viagem (12 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `EmpresaFicha` | int | ? |
| `NrFicha` | int | ? |
| `DtEmissao` | datetime | ? |
| `VlComissao` | numeric(14,4) | ? |
| `CdRotaRMS` | int | ? |
| `MotoristaFV` | nvarchar(100) | ? |
| `ProprietarioFV` | nvarchar(100) | ? |
| `VeiculoFV` | nvarchar(8) | ? |
| `CarretaFV` | nvarchar(8) | ? |
| `Carreta2FV` | nvarchar(8) | ? |
| `Carreta3FV` | nvarchar(8) | ? |
| `UserFicha` | nvarchar(10) | ? |

#### Romaneio / Manifesto (11 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `EmpresaRomaneio` | int | ? |
| `RotaRomaneio` | int | ? |
| `RomaneioManifesto` | nvarchar(20) | ? |
| `UserRomaneioManifesto` | nvarchar(10) | ? |
| `DtEmissaoRmMan` | datetime | ? |
| `VeiculoRmMan` | nvarchar(8) | ? |
| `CarretaRmMan` | nvarchar(8) | ? |
| `Carreta2RmMan` | nvarchar(8) | ? |
| `Carreta3RmMan` | nvarchar(8) | ? |
| `MotoristaRmMan` | nvarchar(40) | ? |
| `ProprietarioRmMan` | nvarchar(40) | ? |

#### CTRB (6 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `NumeroCTRB` | int | ? |
| `UserCtrb` | nvarchar(10) | ? |
| `DtEmissaoCTRB` | datetime | ? |
| `ValorTotalCTRB` | numeric(14,4) | ? |
| `AdtoCTRB` | numeric(14,4) | ? |
| `PedagioCTRB` | numeric(14,4) | ? |

#### CT-e / Documento Fiscal (5 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `EmpresaCTE` | int | ? |
| `NrDoctoFiscal` | int | ? |
| `UserCTE` | nvarchar(10) | ? |
| `TipoDoctoFiscal` | nvarchar(10) | ? |
| `DtEmissaoCTE` | datetime | ? |

#### Valores de Frete (5 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `VlFretePeso` | numeric(14,4) | ? |
| `VlFreteValor` | numeric(14,4) | ? |
| `VlPedagio` | numeric(14,4) | ? |
| `VlGRIS` | numeric(14,4) | ? |
| `VlTotalPrestacao` | numeric(14,4) | ? |

#### Localização (4 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CidadeColeta` | nvarchar(30) | ? |
| `DsUFOrigem` | nvarchar(2) | ? |
| `CidadeEntrega` | nvarchar(30) | ? |
| `DsUFDestino` | nvarchar(2) | ? |

#### Partes Envolvidas (8 colunas)
| Coluna | Tipo | Nulável |
|---|---|:---:|
| `CdRemetente` | nvarchar(14) | ? |
| `DsRemetente` | nvarchar(100) | ? |
| `CdDestinatario` | nvarchar(14) | ? |
| `DsDestinatario` | nvarchar(100) | ? |
| `CdInscricao` | nvarchar(14) | ? |
| `DsPagador` | nvarchar(100) | ? |
| `CdRedespacho` | nvarchar(14) | ? |
| `DsRedespacho` | nvarchar(40) | ? |

---

### Colunas NÃO carregadas por este ETL (40)

Existem no schema da tabela mas **não recebem dados** nesta pipeline:

| Coluna | Tipo |
|---|---|
| `NrDiasAtraso` | int |
| `Status` | nvarchar(14) |
| `DtPrevisaoEntregaCTeNova` | datetime |
| `FichaViagem` | nvarchar(32) |
| `Hora` | datetime |
| `IDCTEPERF` | nvarchar(42) |
| `CNPJRemetente` | nvarchar(14) |
| `NomeRemetente` | nvarchar(100) |
| `NFSerie` | nvarchar(3) |
| `NFNr` | int |
| `NFVlr` | numeric(14,4) |
| `NFDtEmissao` | datetime |
| `CTeEmpresa` | int |
| `CTeNr` | int |
| `CTeDtEmissao` | datetime |
| `DtPrevisaoEntregaCTe` | datetime |
| `DtAgendamentoEntregaCTe` | datetime |
| `CTeDtEntrega` | datetime |
| `CNPJDestinatario` | nvarchar(14) |
| `NomeDestinatario` | nvarchar(100) |
| `CidadeDestino` | nvarchar(30) |
| `UFDestino` | nvarchar(2) |
| `CNPJPagador` | nvarchar(14) |
| `NomePagador` | nvarchar(100) |
| `NrSeqControle` | int |
| `NaturezaCod` | int |
| `NaturezaNome` | nvarchar(40) |
| `TipoTransporte` | nvarchar(40) |
| `Volumes` | numeric(14,4) |
| `QtPesoCubado` | numeric(14,4) |
| `HistEntregaCod` | int |
| `HistEntregaNome` | nvarchar(50) |
| `FichaVUltManifesto` | nvarchar(21) |
| `FichaVUltRom` | nvarchar(32) |
| `DtRomaneio` | datetime |
| `DtManifesto` | datetime |
| `TipoEmissao` | nvarchar(16) |
| `DsGrupoCliente` | nvarchar(100) |
| `CdGrupoCliente` | int |
| `NrRegistros` | int |

> O package foi desenvolvido em 28/05/2025 e é possível que estas colunas foram adicionadas posteriormente à tabela para outros ETLs, ou que a view `Ficha_de_Viagem_Geral` não expõe esses dados ainda.

---

## Configurações de Performance

| Configuração | Valor | Observação |
|---|---|---|
| `UseBulkInsertWhenPossible` | `true` | Usa SqlBulkCopy para inserção em massa — mais rápido |
| `BatchSize` | `0` | Usa o tamanho do buffer interno do SSIS |
| `CommandTimeout` | `30s` | Pode ser insuficiente para volumes grandes |

---

## Tratamento de Erros no Destino

O componente ADO NET Destination tem um **Error Output** com `ErrorCode` e `ErrorColumn`, mas ele **não está conectado** a nenhum handler. Qualquer falha de inserção (violação de constraint, overflow de tipo, etc.) interrompe o pipeline completamente sem registro do registro problemático.
