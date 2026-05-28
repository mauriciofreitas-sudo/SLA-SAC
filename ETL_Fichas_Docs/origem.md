# Origem dos Dados — ETL_Fichas

## Identificação

| Atributo | Valor |
|---|---|
| **Servidor** | `169.57.181.231` |
| **Banco de dados** | `SOFTRAN_TRANSLUTE` |
| **Objeto de origem** | `dbo.Ficha_de_Viagem_Geral` |
| **Tipo do objeto** | View |
| **Usuário de conexão** | `softran` |
| **Tipo de conexão** | ADO.NET (`System.Data.SqlClient`) |
| **Modo de acesso** | Tabela/View completa (AccessMode = 0) — **sem filtro SQL** |
| **Timeout de comando** | 600 segundos |
| **Implicit String Conversion** | Habilitada |
| **Validação de metadados externos** | **Desabilitada** (`validateExternalMetadata="False"`) |

---

## Diagrama da Origem

```
┌──────────────────────────────────────────────────────────────┐
│  SERVIDOR:  169.57.181.231                                   │
│  BANCO:     SOFTRAN_TRANSLUTE                                │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  VIEW: dbo.Ficha_de_Viagem_Geral                       │  │
│  │                                                        │  │
│  │  Dados consolidados da ficha de viagem do sistema      │  │
│  │  operacional SOFTRAN (TMS)                             │  │
│  │                                                        │  │
│  │  Leitura: FULL SCAN — todos os registros da view       │  │
│  │  Sem WHERE / sem filtro de data                        │  │
│  └──────────────────┬─────────────────────────────────────┘  │
│                     │ 51 colunas exportadas                   │
└─────────────────────┼────────────────────────────────────────┘
                      │
                      ▼
              [Data Flow: Load fFicha]
```

---

## Sistema de Origem

O banco `SOFTRAN_TRANSLUTE` é o sistema transacional **SOFTRAN** — TMS (Transportation Management System) utilizado para gestão de fretes, fichas de viagem, CT-e, romaneios e manifestos.

A view `dbo.Ficha_de_Viagem_Geral` consolida dados de múltiplas entidades do SOFTRAN em uma única estrutura desnormalizada para facilitar a extração.

---

## Colunas Exportadas pela Origem (51 colunas)

### Grupo 1 — Ficha de Viagem

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `EmpresaFicha` | int | — | Código da empresa da ficha |
| `NrFicha` | int | — | Número da ficha de viagem |
| `DtEmissao` | datetime | — | Data de emissão da ficha |
| `VlComissao` | numeric | 14,4 | Valor da comissão |
| `CdRotaRMS` | int | — | Código da rota no RMS |
| `MotoristaFV` | nvarchar | 100 | Nome do motorista da ficha de viagem |
| `ProprietarioFV` | nvarchar | 100 | Nome do proprietário do veículo |
| `VeiculoFV` | nvarchar | 8 | Placa do veículo (cavalo) |
| `CarretaFV` | nvarchar | 8 | Placa da carreta 1 |
| `Carreta2FV` | nvarchar | 8 | Placa da carreta 2 |
| `Carreta3FV` | nvarchar | 8 | Placa da carreta 3 |
| `UserFicha` | nvarchar | 10 | Usuário que criou a ficha |

### Grupo 2 — Romaneio / Manifesto

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `EmpresaRomaneio` | int | — | Código da empresa do romaneio |
| `RotaRomaneio` | int | — | Código da rota do romaneio |
| `RomaneioManifesto` | nvarchar | 20 | Número do romaneio/manifesto |
| `UserRomaneioManifesto` | nvarchar | 10 | Usuário do romaneio/manifesto |
| `DtEmissaoRmMan` | datetime | — | Data de emissão do romaneio/manifesto |
| `VeiculoRmMan` | nvarchar | 8 | Placa do veículo no romaneio |
| `CarretaRmMan` | nvarchar | 8 | Placa da carreta 1 no romaneio |
| `Carreta2RmMan` | nvarchar | 8 | Placa da carreta 2 no romaneio |
| `Carreta3RmMan` | nvarchar | 8 | Placa da carreta 3 no romaneio |
| `MotoristaRmMan` | nvarchar | 40 | Motorista no romaneio/manifesto |
| `ProprietarioRmMan` | nvarchar | 40 | Proprietário no romaneio/manifesto |

### Grupo 3 — CTRB (Contrato de Transporte Rodoviário de Bens)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `NumeroCTRB` | int | — | Número do CTRB |
| `UserCtrb` | nvarchar | 10 | Usuário do CTRB |
| `DtEmissaoCTRB` | datetime | — | Data de emissão do CTRB |
| `ValorTotalCTRB` | numeric | 14,4 | Valor total do CTRB |
| `AdtoCTRB` | numeric | 14,4 | Valor de adiantamento do CTRB |
| `PedagioCTRB` | numeric | 14,4 | Valor de pedágio do CTRB |

### Grupo 4 — CT-e (Conhecimento de Transporte Eletrônico)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `EmpresaCTE` | int | — | Código da empresa do CT-e |
| `NrDoctoFiscal` | int | — | Número do documento fiscal |
| `UserCTE` | nvarchar | 10 | Usuário do CT-e |
| `TipoDoctoFiscal` | nvarchar | 10 | Tipo do documento fiscal |
| `DtEmissaoCTE` | datetime | — | Data de emissão do CT-e |

### Grupo 5 — Valores de Frete

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `VlFretePeso` | numeric | 14,4 | Frete por peso |
| `VlFreteValor` | numeric | 14,4 | Frete por valor (ad valorem) |
| `VlPedagio` | numeric | 14,4 | Valor de pedágio |
| `VlGRIS` | numeric | 14,4 | GRIS (Gerenciamento de Risco) |
| `VlTotalPrestacao` | numeric | 14,4 | Valor total da prestação de serviço |

### Grupo 6 — Localização (Origem/Destino)

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CidadeColeta` | nvarchar | 30 | Cidade de coleta |
| `DsUFOrigem` | nvarchar | 2 | UF de origem |
| `CidadeEntrega` | nvarchar | 30 | Cidade de entrega |
| `DsUFDestino` | nvarchar | 2 | UF de destino |

### Grupo 7 — Partes Envolvidas

| Coluna | Tipo SSIS | Tamanho | Descrição |
|---|---|---|---|
| `CdRemetente` | nvarchar | 14 | CNPJ/CPF do remetente |
| `DsRemetente` | nvarchar | 100 | Nome do remetente |
| `CdDestinatario` | nvarchar | 14 | CNPJ/CPF do destinatário |
| `DsDestinatario` | nvarchar | 100 | Nome do destinatário |
| `CdInscricao` | nvarchar | 14 | CNPJ/CPF do pagador (inscrição) |
| `DsPagador` | nvarchar | 100 | Nome do pagador do frete |
| `CdRedespacho` | nvarchar | 14 | CNPJ/CPF do redespacho |
| `DsRedespacho` | nvarchar | 40 | Nome do redespacho |

---

## Tratamento de Erros na Origem

O componente ADO NET Source possui um **Error Output** configurado, mas ele **não está conectado** a nenhum handler. Em caso de falha de conversão de dados, o pipeline falha completamente sem registro dos registros problemáticos.

---

## Observações Importantes

1. **Sem filtro temporal**: A view é lida por completo. Todos os registros são extraídos independente da data — a variável `User::StartDate` (01/01/2025) **não é aplicada** como filtro na origem.

2. **Validação desabilitada**: `validateExternalMetadata="False"` significa que o SSIS não verifica em design-time se o schema da view ainda corresponde ao mapeamento do package. Alterações na view podem não ser detectadas até a execução.

3. **Timeout generoso**: 600 segundos (10 minutos) sugere que a view `Ficha_de_Viagem_Geral` pode ser pesada — provavelmente faz JOINs entre múltiplas tabelas do SOFTRAN.
