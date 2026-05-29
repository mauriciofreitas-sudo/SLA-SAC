# Origem dos Dados — ETL_BaseCTE

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `169.57.181.231` |
| **Banco de dados** | `SOFTRAN_TRANSLUTE` |
| **Objeto de origem** | `dbo.vwCTE_Base` |
| **Tipo do objeto** | View |
| **Usuário de conexão** | `softran` |
| **Tipo de conexão** | ADO.NET (`System.Data.SqlClient`) |
| **Modo de acesso** | Tabela/View completa (AccessMode = 0) — sem filtro SQL |
| **Timeout de comando** | 600 segundos |
| **Implicit String Conversion** | Habilitada |
| **Validação de metadados externos** | Desabilitada (`validateExternalMetadata="False"`) |

---

## Diagrama da Origem

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  169.57.181.231                                   │
│  BANCO:     SOFTRAN_TRANSLUTE                                │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  VIEW: dbo.vwCTE_Base                                  │  │
│  │                                                        │  │
│  │  Dados consolidados de CT-e do sistema SOFTRAN (TMS)   │  │
│  │  Inclui: filial, UF, remetente, destinatário,          │  │
│  │  receita, frete, margem, vínculo do motorista          │  │
│  │                                                        │  │
│  │  Leitura: FULL SCAN — todos os registros da view       │  │
│  │  Sem WHERE / sem filtro de data                        │  │
│  └──────────────────┬─────────────────────────────────────┘  │
│                     │ colunas exportadas                      │
└─────────────────────┼────────────────────────────────────────┘
                      │
                      ▼
              [Data Flow: Load fBaseCTE]
```

---

## Sistema de Origem

O banco `SOFTRAN_TRANSLUTE` é o sistema transacional **SOFTRAN** — TMS utilizado para gestão de fretes, CT-e, romaneios e manifestos. A view `dbo.vwCTE_Base` consolida dados de múltiplas tabelas do SOFTRAN em uma estrutura desnormalizada para extração.

---

## Colunas Exportadas pela Origem

### Grupo 1 — Identificação do CT-e

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `EmpresaCTE` | int | — | Código da empresa emissora do CT-e |
| `NrDoctoFiscal` | int | — | Número do documento fiscal (CT-e) |
| `TipoDoctoFiscal` | nvarchar | 10 | Tipo: `C` (Carga) ou `F` (Fracionado) |
| `DtEmissao` | datetime | — | Data de emissão do CT-e |
| `AnoMes` | int | — | Competência YYYYMM |
| `Ano` | int | — | Ano de emissão |
| `Mes` | int | — | Mês de emissão (1–12) |

### Grupo 2 — Filial e Localização

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `Filial` | nvarchar | 10 | Código da filial (CGR, NOD, BAR, RES, SSA, SBC, POA, REC, CWB, LC2, VIX) |
| `DsFilial` | nvarchar | 100 | Descrição da filial |
| `UFOrigem` | nvarchar | 2 | UF de origem da carga |
| `CidadeOrigem` | nvarchar | 50 | Cidade de origem |
| `UFDestino` | nvarchar | 2 | UF de destino da carga |
| `CidadeDestino` | nvarchar | 50 | Cidade de destino |

### Grupo 3 — Partes Envolvidas

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CdRemetente` | nvarchar | 14 | CNPJ/CPF do remetente |
| `DsRemetente` | nvarchar | 100 | Nome do remetente |
| `CdDestinatario` | nvarchar | 14 | CNPJ/CPF do destinatário |
| `DsDestinatario` | nvarchar | 100 | Nome do destinatário |
| `CdPagador` | nvarchar | 14 | CNPJ/CPF do pagador |
| `DsPagador` | nvarchar | 100 | Nome do pagador |
| `CdGrupoCliente` | int | — | Código do grupo de cliente |
| `DsGrupoCliente` | nvarchar | 100 | Nome do grupo de cliente |

### Grupo 4 — Motorista e Veículo

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CdMotorista` | nvarchar | 14 | CPF do motorista |
| `DsMotorista` | nvarchar | 100 | Nome do motorista |
| `VinculoMotorista` | nvarchar | 20 | `Terceiro` ou `Funcionario` |
| `Veiculo` | nvarchar | 8 | Placa do cavalo |
| `Carreta` | nvarchar | 8 | Placa da carreta |

### Grupo 5 — Valores Financeiros

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlReceita` | numeric | 14,4 | Receita total cobrada do cliente |
| `VlFrete` | numeric | 14,4 | Custo de frete pago ao motorista/transportadora |
| `VlMargem` | numeric | 14,4 | Margem = VlReceita − VlFrete |
| `PctMargem` | numeric | 5,2 | Margem % = VlMargem / VlReceita × 100 |
| `VlFretePeso` | numeric | 14,4 | Frete por peso |
| `VlFreteValor` | numeric | 14,4 | Frete por valor declarado (ad valorem) |
| `VlPedagio` | numeric | 14,4 | Pedágio |
| `VlGRIS` | numeric | 14,4 | Gerenciamento de Risco |
| `VlTotalPrestacao` | numeric | 14,4 | Valor total da prestação |

### Grupo 6 — Ficha e CTRB Associados

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `NrFicha` | int | — | Número da ficha de viagem associada |
| `NrCTRB` | int | — | Número do CTRB associado |
| `DtEmissaoCTRB` | datetime | — | Data de emissão do CTRB |

---

## Observações Importantes

1. **Sem filtro temporal**: Full scan de todos os registros históricos a cada execução.
2. **Validação desabilitada**: Mudanças de schema na view só são detectadas em runtime.
3. **Abrangência histórica**: Deve cobrir 2024, 2025 e 2026 para o full-refresh ser consistente com o dashboard.
