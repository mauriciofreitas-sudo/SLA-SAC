# ETL_Romaneios1 — Destino

## Servidor de Destino

| Propriedade            | Valor                          |
|------------------------|--------------------------------|
| Servidor               | 10.100.86.89                   |
| Banco de Dados         | DWGrupolc                      |
| Usuário                | sqldba                         |
| Tipo de Conexão        | ADO.NET (SqlClient)            |
| TrustServerCertificate | True                           |

## Destination Component: ADO NET Destination

| Propriedade               | Valor                     |
|---------------------------|---------------------------|
| Nome do Componente        | ADO NET Destination        |
| Tabela Destino            | `fFichaViagem`            |
| BatchSize                 | 0 (usa buffer interno SSIS)|
| CommandTimeout            | 30 segundos               |
| UseBulkInsertWhenPossible | true                      |

## Estratégia de Carga

**DELETE + INSERT (Full Reload)**

1. Task `Truncate fFicha` executa `DELETE FROM dbo.fFichaViagem` sem WHERE (apaga tudo)
2. Task `Load fFicha` insere todos os registros via ADO.NET Bulk Insert

## Schema da Tabela Destino `fFichaViagem`

Colunas mapeadas pelo pipeline (51 colunas efetivamente carregadas):

| # | Coluna                   | Tipo SSIS      | Tamanho / Precisão        | Nulável |
|---|--------------------------|----------------|---------------------------|---------|
| 1  | EmpresaFicha            | i4 (int)       | —                         | Sim     |
| 2  | NrFicha                 | i4 (int)       | —                         | Sim     |
| 3  | DtEmissao               | dbTimeStamp    | —                         | Sim     |
| 4  | VlComissao              | numeric        | p14, s4                   | Sim     |
| 5  | CdRotaRMS               | i4 (int)       | —                         | Sim     |
| 6  | MotoristaFV             | wstr           | 100                       | Sim     |
| 7  | ProprietarioFV          | wstr           | 100                       | Sim     |
| 8  | VeiculoFV               | wstr           | 8                         | Sim     |
| 9  | CarretaFV               | wstr           | 8                         | Sim     |
| 10 | Carreta2FV              | wstr           | 8                         | Sim     |
| 11 | Carreta3FV              | wstr           | 8                         | Sim     |
| 12 | UserFicha               | wstr           | 10                        | Sim     |
| 13 | EmpresaRomaneio         | i4 (int)       | —                         | Sim     |
| 14 | RotaRomaneio            | i4 (int)       | —                         | Sim     |
| 15 | RomaneioManifesto       | wstr           | 20                        | Sim     |
| 16 | UserRomaneioManifesto   | wstr           | 10                        | Sim     |
| 17 | DtEmissaoRmMan          | dbTimeStamp    | —                         | Sim     |
| 18 | VeiculoRmMan            | wstr           | 8                         | Sim     |
| 19 | CarretaRmMan            | wstr           | 8                         | Sim     |
| 20 | Carreta2RmMan           | wstr           | 8                         | Sim     |
| 21 | Carreta3RmMan           | wstr           | 8                         | Sim     |
| 22 | MotoristaRmMan          | wstr           | 40                        | Sim     |
| 23 | ProprietarioRmMan       | wstr           | 40                        | Sim     |
| 24 | NumeroCTRB              | i4 (int)       | —                         | Sim     |
| 25 | UserCtrb                | wstr           | 10                        | Sim     |
| 26 | DtEmissaoCTRB           | dbTimeStamp    | —                         | Sim     |
| 27 | ValorTotalCTRB          | numeric        | p14, s4                   | Sim     |
| 28 | AdtoCTRB                | numeric        | p14, s4                   | Sim     |
| 29 | PedagioCTRB             | numeric        | p14, s4                   | Sim     |
| 30 | EmpresaCTE              | i4 (int)       | —                         | Sim     |
| 31 | VlFretePeso             | numeric        | p14, s4                   | Sim     |
| 32 | VlFreteValor            | numeric        | p14, s4                   | Sim     |
| 33 | VlPedagio               | numeric        | p14, s4                   | Sim     |
| 34 | VlGRIS                  | numeric        | p14, s4                   | Sim     |
| 35 | VlTotalPrestacao        | numeric        | p14, s4                   | Sim     |
| 36 | NrDoctoFiscal           | i4 (int)       | —                         | Sim     |
| 37 | UserCTE                 | wstr           | 10                        | Sim     |
| 38 | TipoDoctoFiscal         | wstr           | 10                        | Sim     |
| 39 | DtEmissaoCTE            | dbTimeStamp    | —                         | Sim     |
| 40 | CidadeColeta            | wstr           | 30                        | Sim     |
| 41 | DsUFOrigem              | wstr           | 2                         | Sim     |
| 42 | CidadeEntrega           | wstr           | 30                        | Sim     |
| 43 | DsUFDestino             | wstr           | 2                         | Sim     |
| 44 | CdRemetente             | wstr           | 14                        | Sim     |
| 45 | DsRemetente             | wstr           | 100                       | Sim     |
| 46 | CdDestinatario          | wstr           | 14                        | Sim     |
| 47 | DsDestinatario          | wstr           | 100                       | Sim     |
| 48 | CdInscricao             | wstr           | 14                        | Sim     |
| 49 | DsPagador               | wstr           | 100                       | Sim     |
| 50 | CdRedespacho            | wstr           | 14                        | Sim     |
| 51 | DsRedespacho            | wstr           | 40                        | Sim     |

## Colunas Adicionais na Tabela (NÃO mapeadas pelo pipeline)

As seguintes colunas existem no schema da tabela `fFichaViagem` mas não recebem dados desta carga (ficam NULL ou com valor default após o DELETE):

| Coluna                      | Tipo SSIS      | Tamanho / Precisão |
|-----------------------------|----------------|--------------------|
| NrDiasAtraso                | i4             | —                  |
| Status                      | wstr           | 14                 |
| DtPrevisaoEntregaCTeNova    | dbTimeStamp    | —                  |
| FichaViagem                 | wstr           | 32                 |
| Hora                        | dbTimeStamp    | —                  |
| IDCTEPERF                   | wstr           | 42                 |
| CNPJRemetente               | wstr           | 14                 |
| NomeRemetente               | wstr           | 100                |
| NFSerie                     | wstr           | 3                  |
| NFNr                        | i4             | —                  |
| NFVlr                       | numeric        | p14, s4            |
| NFDtEmissao                 | dbTimeStamp    | —                  |
| CTeEmpresa                  | i4             | —                  |
| CTeNr                       | i4             | —                  |
| CTeDtEmissao                | dbTimeStamp    | —                  |
| DtPrevisaoEntregaCTe        | dbTimeStamp    | —                  |
| DtAgendamentoEntregaCTe     | dbTimeStamp    | —                  |
| CTeDtEntrega                | dbTimeStamp    | —                  |
| CNPJDestinatario            | wstr           | 14                 |
| NomeDestinatario            | wstr           | 100                |
| CidadeDestino               | wstr           | 30                 |
| UFDestino                   | wstr           | 2                  |
| CNPJPagador                 | wstr           | 14                 |
| NomePagador                 | wstr           | 100                |
| NrSeqControle               | i4             | —                  |
| NaturezaCod                 | i4             | —                  |
| NaturezaNome                | wstr           | 40                 |
| TipoTransporte              | wstr           | 40                 |
| Volumes                     | numeric        | p14, s4            |
| QtPesoCubado                | numeric        | p14, s4            |
| HistEntregaCod              | i4             | —                  |
| HistEntregaNome             | wstr           | 50                 |
| FichaVUltManifesto          | wstr           | 21                 |
| FichaVUltRom                | wstr           | 32                 |
| DtRomaneio                  | dbTimeStamp    | —                  |
| DtManifesto                 | dbTimeStamp    | —                  |
| TipoEmissao                 | wstr           | 16                 |
| DsGrupoCliente              | wstr           | 100                |
| CdGrupoCliente              | i4             | —                  |
| NrRegistros                 | i4             | —                  |
