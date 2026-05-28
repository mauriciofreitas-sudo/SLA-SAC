# Transformações — ETL_Fichas

## Resumo

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   ORIGEM                                          DESTINO        │
│   dbo.Ficha_de_Viagem_Geral   ────────────►   dbo.fFichaViagem   │
│                                                                  │
│              PASSTHROUGH DIRETO                                  │
│              Nenhuma transformação aplicada                      │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

O pipeline **não contém transformações**. Os dados são transferidos diretamente da origem para o destino sem qualquer componente de transformação intermediário:

- Sem `Derived Column`
- Sem `Data Conversion`
- Sem `Lookup`
- Sem `Conditional Split`
- Sem `Aggregate`
- Sem `Sort`
- Sem `Script Component`

---

## Componentes do Data Flow

```
┌─────────────────────┐         ┌─────────────────────────┐
│   ADO NET Source    │         │   ADO NET Destination   │
│                     │         │                         │
│ • 169.57.181.231    │         │ • 10.100.86.89          │
│ • SOFTRAN_TRANSLUTE │─────────│ • DWGrupolc             │
│ • Ficha_de_Viagem   │         │ • fFichaViagem          │
│   _Geral (view)     │         │                         │
└─────────────────────┘         └─────────────────────────┘
        Source                          Destination
   (sem transformação)
```

**Componentes presentes:**
| Posição | Componente | Tipo | Função |
|:---:|---|---|---|
| 1 | ADO NET Source | Source Adapter | Lê dados da view de origem |
| 2 | ADO NET Destination | Destination Adapter | Grava dados na tabela de destino |

Não há nenhum componente entre Source e Destination.

---

## Pré-processamento (fora do Data Flow)

A única "transformação" acontece **antes** do Data Flow, em uma task separada:

### Execute SQL Task — "Truncate fFicha"

```sql
DELETE FROM dbo.fFichaViagem
```

| Atributo | Valor |
|---|---|
| Servidor | 10.100.86.89 (DWGrupolc) |
| Conexão | OLE DB (`sqldba1`) |
| Efeito | Remove **todos** os registros da tabela de destino |
| Estratégia | Full-refresh (apaga tudo, recarrega tudo) |

> Esta operação garante que a tabela de destino reflita exatamente o estado atual da view de origem, sem registros duplicados ou obsoletos.

---

## Mapeamento de Tipos de Dados

Todos os 51 campos são mapeados diretamente sem conversão de tipo. A correspondência entre os tipos da fonte e do destino é exata:

| Tipo na Origem (ADO.NET) | Tipo SSIS | Tipo esperado no Destino |
|---|---|---|
| `int` | `DT_I4` | `int` |
| `datetime` / `datetime2` | `DT_DBTIMESTAMP` | `datetime` |
| `decimal` / `numeric` | `DT_NUMERIC (14,4)` | `numeric(14,4)` |
| `nvarchar(n)` | `DT_WSTR (n)` | `nvarchar(n)` |

---

## Campos que NÃO são carregados

A tabela `dbo.fFichaViagem` possui **40 colunas adicionais** que existem no destino mas **não recebem dados** desta pipeline. Elas permanecerão com o valor `NULL` (ou o default da coluna) após a carga:

| Coluna | Tipo | Possível Origem Alternativa |
|---|---|---|
| `NrDiasAtraso` | int | Calculado por outra rotina? |
| `Status` | nvarchar(14) | Calculado por outra rotina? |
| `DtPrevisaoEntregaCTeNova` | datetime | Outro ETL de CT-e? |
| `FichaViagem` | nvarchar(32) | Chave concatenada? |
| `Hora` | datetime | Timestamp de carga? |
| `IDCTEPERF` | nvarchar(42) | ID externo do CT-e? |
| `CNPJRemetente` | nvarchar(14) | Outro ETL? |
| `NomeRemetente` | nvarchar(100) | Outro ETL? |
| `NFSerie` | nvarchar(3) | Dados de NF Fiscal |
| `NFNr` | int | Número da NF |
| `NFVlr` | numeric(14,4) | Valor da NF |
| `NFDtEmissao` | datetime | Data emissão NF |
| `CTeEmpresa` | int | Empresa do CT-e |
| `CTeNr` | int | Número do CT-e |
| `CTeDtEmissao` | datetime | Data emissão CT-e |
| `DtPrevisaoEntregaCTe` | datetime | Previsão de entrega |
| `DtAgendamentoEntregaCTe` | datetime | Data agendamento |
| `CTeDtEntrega` | datetime | Data real de entrega |
| `CNPJDestinatario` | nvarchar(14) | — |
| `NomeDestinatario` | nvarchar(100) | — |
| `CidadeDestino` | nvarchar(30) | — |
| `UFDestino` | nvarchar(2) | — |
| `CNPJPagador` | nvarchar(14) | — |
| `NomePagador` | nvarchar(100) | — |
| `NrSeqControle` | int | Sequencial de controle |
| `NaturezaCod` | int | Código da natureza da carga |
| `NaturezaNome` | nvarchar(40) | Descrição da natureza |
| `TipoTransporte` | nvarchar(40) | Modalidade de transporte |
| `Volumes` | numeric(14,4) | Quantidade de volumes |
| `QtPesoCubado` | numeric(14,4) | Peso cubado |
| `HistEntregaCod` | int | Código histórico de entrega |
| `HistEntregaNome` | nvarchar(50) | Descrição do histórico |
| `FichaVUltManifesto` | nvarchar(21) | Último manifesto da ficha |
| `FichaVUltRom` | nvarchar(32) | Último romaneio da ficha |
| `DtRomaneio` | datetime | Data do romaneio |
| `DtManifesto` | datetime | Data do manifesto |
| `TipoEmissao` | nvarchar(16) | Tipo de emissão |
| `DsGrupoCliente` | nvarchar(100) | Nome do grupo de cliente |
| `CdGrupoCliente` | int | Código do grupo de cliente |
| `NrRegistros` | int | Contagem de registros |

> Estas 40 colunas provavelmente são preenchidas por outros packages ETL ou por procedures que rodam após esta carga.
