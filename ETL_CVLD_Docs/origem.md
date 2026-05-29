# Origem dos Dados — ETL_CVLD

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `169.57.181.231` |
| **Banco de dados** | `SOFTRAN_TRANSLUTE` |
| **Objeto de origem** | `dbo.VW_CVLD` |
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
│  │  VIEW: dbo.VW_CVLD                                     │  │
│  │                                                        │  │
│  │  Dados de Custo CTRB (Contrato de Transporte           │  │
│  │  Rodoviário de Bens) — terceiros e motoristas          │  │
│  │                                                        │  │
│  │  Inclui: transportadora, frete pago, saldo,            │  │
│  │  pedágio, adiantamento, aluguel de carreta,            │  │
│  │  encargos (INSS, SEST/SENAT, IRRF)                     │  │
│  │                                                        │  │
│  │  Leitura: FULL SCAN — sem filtro de data               │  │
│  └──────────────────┬─────────────────────────────────────┘  │
│                     │ colunas exportadas                      │
└─────────────────────┼────────────────────────────────────────┘
                      │
                      ▼
              [Data Flow: Load fCVLD]
```

---

## Sistema de Origem

`VW_CVLD` é uma view do SOFTRAN que consolida os dados financeiros de **Contratos de Transporte Rodoviário de Bens (CTRB)**, abrangendo pagamentos a transportadoras terceirizadas e motoristas. Inclui a composição completa do custo: frete, pedágio, adiantamentos, encargos previdenciários e aluguel de carretas.

---

## Colunas Exportadas pela Origem

### Grupo 1 — Identificação do CTRB

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `NumeroCTRB` | int | — | Número do CTRB |
| `EmpresaCTRB` | int | — | Código da empresa do CTRB |
| `DtEmissaoCTRB` | datetime | — | Data de emissão do CTRB |
| `DtPagamentoCTRB` | datetime | — | Data de pagamento |
| `StatusCTRB` | nvarchar | 20 | Status atual (Pago, Pendente, Cancelado) |
| `AnoMes` | int | — | Competência YYYYMM |

### Grupo 2 — Filial e Localização

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `Filial` | nvarchar | 10 | Código da filial (CGR, NOD, BAR, etc.) |
| `UFOrigem` | nvarchar | 2 | UF de origem da viagem |
| `UFDestino` | nvarchar | 2 | UF de destino da viagem |

### Grupo 3 — Transportadora / Motorista

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CdTransportadora` | nvarchar | 14 | CNPJ/CPF da transportadora |
| `DsTransportadora` | nvarchar | 100 | Nome da transportadora |
| `CdMotorista` | nvarchar | 14 | CPF do motorista |
| `DsMotorista` | nvarchar | 100 | Nome do motorista |
| `TipoVinculo` | nvarchar | 20 | `Terceiro` ou `Funcionario` |
| `Veiculo` | nvarchar | 8 | Placa do cavalo |
| `Carreta` | nvarchar | 8 | Placa da carreta principal |

### Grupo 4 — Ficha de Viagem

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `NrFicha` | int | — | Número da ficha de viagem |
| `DtEmissaoFicha` | datetime | — | Data de emissão da ficha |

### Grupo 5 — Valores do CTRB

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlFreteCTRB` | numeric | 14,4 | Valor total do frete do CTRB |
| `VlSaldoCTRB` | numeric | 14,4 | Saldo a pagar ao motorista/transportadora |
| `VlAdiantamentoCTRB` | numeric | 14,4 | Adiantamento pago |
| `VlPedagioCTRB` | numeric | 14,4 | Pedágio incluído no CTRB |

### Grupo 6 — Encargos e Aluguel

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlINSS` | numeric | 14,4 | Contribuição INSS retido |
| `VlSESTSENAT` | numeric | 14,4 | Contribuição SEST/SENAT |
| `VlIRRF` | numeric | 14,4 | IRRF retido na fonte |
| `VlAluguelCarreta` | numeric | 14,4 | Valor de aluguel de carreta |
| `FlAluguel` | bit | — | Flag: CTRB possui aluguel de carreta (1/0) |

---

## Observações Importantes

1. **Encargos previdenciários**: INSS, SEST/SENAT e IRRF são retidos na fonte pelo embarcador e repassados ao governo — esses valores compõem o custo total do CTRB.

2. **Aluguel de carreta**: Quando `FlAluguel = 1`, o CTRB inclui cobrança por uso de carreta da empresa — relevante para o KPI "R$ 151.250 em aluguel (Jan–Mai/2026)".

3. **Composição identificada no dashboard** (Jan–Mai/2026):

| Componente | Valor apurado |
|---|---|
| Frete Motorista | R$ 136.429.967 |
| Pedágio | R$ 11.222.280 |
| INSS | R$ 1.711.678 |
| SEST/SENAT | R$ 406.389 |
| Aluguel Carretas | R$ 151.250 |
| IRRF | R$ 107.521 |
| **Total CTRB** | **R$ 150.029.085** |
