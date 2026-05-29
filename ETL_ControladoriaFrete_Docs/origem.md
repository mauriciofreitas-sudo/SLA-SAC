# Origem dos Dados — ETL_ControladoriaFrete

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `169.57.181.231` |
| **Banco de dados** | `SOFTRAN_TRANSLUTE` |
| **Objeto de origem** | `dbo.VW_CONTROLADORIA_DE_FRETE` |
| **Tipo do objeto** | View |
| **Usuário de conexão** | `softran` |
| **Tipo de conexão** | ADO.NET (`System.Data.SqlClient`) |
| **Modo de acesso** | Tabela/View completa (AccessMode = 0) — sem filtro SQL |
| **Timeout de comando** | 600 segundos |
| **Validação de metadados externos** | Desabilitada (`validateExternalMetadata="False"`) |

---

## Diagrama da Origem

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  169.57.181.231                                   │
│  BANCO:     SOFTRAN_TRANSLUTE                                │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  VIEW: dbo.VW_CONTROLADORIA_DE_FRETE                   │  │
│  │                                                        │  │
│  │  Visão consolidada de resultado por operação:          │  │
│  │  CT-e (receita) × CTRB (custo) × Ficha (viagem)       │  │
│  │                                                        │  │
│  │  Inclui: margem por filial, rota, cliente,             │  │
│  │  motorista — vinculando receita e custo                │  │
│  │                                                        │  │
│  │  Leitura: FULL SCAN — sem filtro de data               │  │
│  └──────────────────┬─────────────────────────────────────┘  │
│                     │ colunas exportadas                      │
└─────────────────────┼────────────────────────────────────────┘
                      │
                      ▼
      [Data Flow: Load fControladoriaFrete]
```

---

## Sistema de Origem

`VW_CONTROLADORIA_DE_FRETE` é a view mais complexa do SOFTRAN utilizada neste DW. Ela cruza dados de três entidades principais:

| Entidade | Tabela/View base | Papel |
|---|---|---|
| CT-e | `vwCTE_Base` ou tabelas de CT-e | Receita da operação |
| Ficha de Viagem | `Ficha_de_Viagem_Geral` | Identificador da viagem |
| CTRB | `VW_CVLD` ou tabelas de CTRB | Custo da operação |

O resultado é uma linha por **CT-e vinculado a uma ficha de viagem**, contendo tanto a receita cobrada do cliente quanto o custo pago ao motorista/transportadora.

---

## Colunas Exportadas pela Origem

### Grupo 1 — Identificação da Operação

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `EmpresaCTE` | int | — | Empresa emissora do CT-e |
| `NrDoctoFiscal` | int | — | Número do CT-e |
| `TipoDoctoFiscal` | nvarchar | 10 | Tipo: `C` (Carga) ou `F` (Fracionado) |
| `DtEmissao` | datetime | — | Data de emissão do CT-e |
| `AnoMes` | int | — | Competência YYYYMM |
| `NrFicha` | int | — | Número da ficha de viagem vinculada |
| `NrCTRB` | int | — | Número do CTRB vinculado |

### Grupo 2 — Filial e Rota

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `Filial` | nvarchar | 10 | Filial emissora (CGR, NOD, BAR, etc.) |
| `UFOrigem` | nvarchar | 2 | UF de origem |
| `CidadeOrigem` | nvarchar | 50 | Cidade de origem |
| `UFDestino` | nvarchar | 2 | UF de destino |
| `CidadeDestino` | nvarchar | 50 | Cidade de destino |
| `CdRota` | int | — | Código da rota |

### Grupo 3 — Cliente (Remetente / Destinatário)

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

### Grupo 4 — Motorista e Transportadora

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CdMotorista` | nvarchar | 14 | CPF do motorista |
| `DsMotorista` | nvarchar | 100 | Nome do motorista |
| `CdTransportadora` | nvarchar | 14 | CNPJ da transportadora |
| `DsTransportadora` | nvarchar | 100 | Nome da transportadora |
| `VinculoMotorista` | nvarchar | 20 | `Terceiro` ou `Funcionario` |
| `Veiculo` | nvarchar | 8 | Placa do cavalo |
| `Carreta` | nvarchar | 8 | Placa da carreta |

### Grupo 5 — Receita (CT-e)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlReceita` | numeric | 14,4 | Receita cobrada do cliente |
| `VlFretePeso` | numeric | 14,4 | Componente frete por peso |
| `VlFreteValor` | numeric | 14,4 | Componente frete ad valorem |
| `VlPedagioReceita` | numeric | 14,4 | Pedágio cobrado do cliente |
| `VlGRIS` | numeric | 14,4 | GRIS cobrado |

### Grupo 6 — Custo (CTRB)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlCustoCTRB` | numeric | 14,4 | Custo total do CTRB |
| `VlFreteCTRB` | numeric | 14,4 | Frete pago ao motorista |
| `VlPedagioCTRB` | numeric | 14,4 | Pedágio pago pelo embarcador |
| `VlAdiantamentoCTRB` | numeric | 14,4 | Adiantamento pago |
| `VlSaldoCTRB` | numeric | 14,4 | Saldo a pagar |
| `VlEncargos` | numeric | 14,4 | Total encargos (INSS + SEST/SENAT + IRRF) |
| `VlAluguelCarreta` | numeric | 14,4 | Aluguel de carreta |

### Grupo 7 — Resultado (Margem)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlMargem` | numeric | 14,4 | Margem = VlReceita − VlCustoCTRB |
| `PctMargem` | numeric | 5,2 | Margem % = VlMargem / VlReceita × 100 |

---

## Observações Importantes

1. **View mais pesada do sistema**: cruza CT-e, Ficha e CTRB — o JOIN pode ser complexo. Timeout de 600s é necessário.

2. **Dependência de dados**: a view só produz resultados completos quando existem CTRBs vinculados a fichas de viagem. CTEs sem CTRB associado podem não aparecer na view.

3. **Margem calculada na view**: `VlMargem` e `PctMargem` são cálculos derivados entregues prontos pela view — o ETL apenas move os dados.
