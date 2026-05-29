# ETL_Performance_V2 — Origem dos Dados

## Servidor de Origem

| Item            | Valor                                              |
|-----------------|----------------------------------------------------|
| Servidor        | 169.57.181.231                                     |
| Banco           | SOFTRAN_TRANSLUTE                                  |
| Usuário         | softran                                            |
| Connection Name | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc            |
| Tipo            | ADO.NET (System.Data.SqlClient)                    |
| CommandTimeout  | 30 segundos                                        |

## Source: ADO NET Source (Data Flow "Load fPerf")

- **Componente:** ADO NET Source
- **Modo:** TableOrViewName (AccessMode = 0 — tabela/view direta, sem SQL inline)
- **Objeto de origem:** `"dbo"."vw_fPerformance"` — view no banco SOFTRAN_TRANSLUTE
- **SqlCommand:** vazio (não há SQL inline; lê a view diretamente)

> A view `dbo.vw_fPerformance` é definida no servidor de origem (169.57.181.231). O SSIS lê todos os registros da view sem filtro adicional.

## Tabela de Colunas da Origem (ADO NET Source Output)

| # | Nome da Coluna              | Tipo SSIS    | Comprimento / Precisão |
|---|-----------------------------|--------------|------------------------|
| 1 | NrDiasAtraso                | i4           | —                      |
| 2 | Status                      | wstr         | 14                     |
| 3 | DtPrevisaoEntregaCTeNova    | dbTimeStamp  | —                      |
| 4 | FichaViagem                 | wstr         | 32                     |
| 5 | Hora                        | dbTimeStamp  | —                      |
| 6 | IDCTEPERF                   | wstr         | 42                     |
| 7 | CNPJRemetente               | wstr         | 14                     |
| 8 | NomeRemetente               | wstr         | 100                    |
| 9 | NFSerie                     | wstr         | 3                      |
| 10 | NFNr                       | i4           | —                      |
| 11 | NFVlr                      | numeric      | p=14, s=4              |
| 12 | NFDtEmissao                | dbTimeStamp  | —                      |
| 13 | CTeEmpresa                 | i4           | —                      |
| 14 | CTeNr                      | i4           | —                      |
| 15 | CTeDtEmissao               | dbTimeStamp  | —                      |
| 16 | TipoDoctoFiscal            | i4           | —                      |
| 17 | DtPrevisaoEntregaCTe       | dbTimeStamp  | —                      |
| 18 | DtAgendamentoEntregaCTe    | dbTimeStamp  | —                      |
| 19 | CTeDtEntrega               | dbTimeStamp  | —                      |
| 20 | CNPJDestinatario           | wstr         | 14                     |
| 21 | NomeDestinatario           | wstr         | 100                    |
| 22 | CidadeDestino              | wstr         | 30                     |
| 23 | UFDestino                  | wstr         | 2                      |
| 24 | CNPJPagador                | wstr         | 14                     |
| 25 | NomePagador                | wstr         | 100                    |
| 26 | NrSeqControle              | i4           | —                      |
| 27 | NaturezaCod                | i4           | —                      |
| 28 | NaturezaNome               | wstr         | 40                     |
| 29 | TipoTransporte             | wstr         | 40                     |
| 30 | Volumes                    | numeric      | p=14, s=4              |
| 31 | QtPesoCubado               | numeric      | p=14, s=4              |
| 32 | HistEntregaCod             | i4           | —                      |
| 33 | HistEntregaNome            | wstr         | 50                     |
| 34 | FichaVUltManifesto         | wstr         | 21                     |
| 35 | FichaVUltRom               | wstr         | 32                     |
| 36 | DtRomaneio                 | dbTimeStamp  | —                      |
| 37 | DtManifesto                | dbTimeStamp  | —                      |
| 38 | TipoEmissao                | wstr         | 16                     |
| 39 | DsGrupoCliente             | wstr         | 100                    |
| 40 | CdGrupoCliente             | i4           | —                      |
| 41 | DsRedespacho               | wstr         | 40                     |
| 42 | CdRedespacho               | wstr         | 14                     |
| 43 | NrRegistros                | i4           | —                      |

**Total: 43 colunas**

## Objeto de Origem — View dbo.vw_fPerformance

A view é definida no servidor SOFTRAN e não está documentada no arquivo SSIS (sem SQL inline). O SSIS consome todos os registros da view sem filtro adicional.

Para consultar a definição da view, executar no servidor de origem:
```sql
-- Executar em: 169.57.181.231 / SOFTRAN_TRANSLUTE
SELECT OBJECT_DEFINITION(OBJECT_ID('dbo.vw_fPerformance'));
```
