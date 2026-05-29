# ETL_Romaneios — Destino dos Dados

## Servidor de Destino

| Item              | Valor                                              |
|-------------------|----------------------------------------------------|
| Servidor          | 10.100.86.89                                       |
| Banco             | DWGrupolc                                          |
| Usuário           | sqldba                                             |
| Connection Name   | 10.100.86.89.DWGrupolc.datalc (ADO.NET)            |
| Connection OLEDB  | 10.100.86.89.DWGrupolc.sqldba1 (OLEDB/SQLNCLI11.1) |

## Estratégia de Carga

| Item                    | Detalhe                                                          |
|-------------------------|------------------------------------------------------------------|
| Estratégia              | Full Reload (DELETE + INSERT)                                    |
| Pré-processamento       | `DELETE FROM dbo.fFichaViagem` (task "Truncate fFicha")          |
| Método de insert        | ADO.NET com SqlBulkCopy habilitado                               |
| BatchSize               | 0 (usa buffer interno do SSIS — auto)                            |
| CommandTimeout destino  | 30 segundos                                                      |
| validateExternalMetadata| não declarado (padrão True para ADO.NET Destination sem flag)    |

## Tabela de Destino: `dbo.fFichaViagem` (banco DWGrupolc)

| # | Nome da Coluna         | Tipo Destino (External) | Precisão/Escala/Tamanho |
|---|------------------------|-------------------------|-------------------------|
| 1 | EmpresaFicha           | i4                      | —                       |
| 2 | NrFicha                | i4                      | —                       |
| 3 | DtEmissao              | dbTimeStamp             | —                       |
| 4 | VlComissao             | numeric                 | p=14, s=4               |
| 5 | CdRotaRMS              | i4                      | —                       |
| 6 | MotoristaFV            | wstr                    | 100                     |
| 7 | ProprietarioFV         | wstr                    | 100                     |
| 8 | VeiculoFV              | wstr                    | 8                       |
| 9 | CarretaFV              | wstr                    | 8                       |
| 10 | Carreta2FV            | wstr                    | 8                       |
| 11 | Carreta3FV            | wstr                    | 8                       |
| 12 | UserFicha             | wstr                    | 10                      |
| 13 | EmpresaRomaneio       | i4                      | —                       |
| 14 | RotaRomaneio          | i4                      | —                       |
| 15 | RomaneioManifesto     | wstr                    | 20                      |
| 16 | UserRomaneioManifesto | wstr                    | 10                      |
| 17 | DtEmissaoRmMan        | dbTimeStamp             | —                       |
| 18 | VeiculoRmMan          | wstr                    | 8                       |
| 19 | CarretaRmMan          | wstr                    | 8                       |
| 20 | Carreta2RmMan         | wstr                    | 8                       |
| 21 | Carreta3RmMan         | wstr                    | 8                       |
| 22 | MotoristaRmMan        | wstr                    | 40                      |
| 23 | ProprietarioRmMan     | wstr                    | 40                      |
| 24 | NumeroCTRB            | i4                      | —                       |
| 25 | UserCtrb              | wstr                    | 10                      |
| 26 | DtEmissaoCTRB         | dbTimeStamp             | —                       |
| 27 | ValorTotalCTRB        | numeric                 | p=14, s=4               |
| 28 | AdtoCTRB              | numeric                 | p=14, s=4               |
| 29 | PedagioCTRB           | numeric                 | p=14, s=4               |
| 30 | EmpresaCTE            | i4                      | —                       |
| 31 | VlFretePeso           | numeric                 | p=14, s=4               |
| 32 | VlFreteValor          | numeric                 | p=14, s=4               |
| 33 | VlPedagio             | numeric                 | p=14, s=4               |
| 34 | VlGRIS                | numeric                 | p=14, s=4               |
| 35 | VlTotalPrestacao      | numeric                 | p=14, s=4               |
| 36 | NrDoctoFiscal         | i4                      | —                       |
| 37 | UserCTE               | wstr                    | 10                      |
| 38 | TipoDoctoFiscal       | wstr                    | 10                      |
| 39 | DtEmissaoCTE          | dbTimeStamp             | —                       |
| 40 | CidadeColeta          | wstr                    | 30                      |
| 41 | DsUFOrigem            | wstr                    | 2                       |
| 42 | CidadeEntrega         | wstr                    | 30                      |
| 43 | DsUFDestino           | wstr                    | 2                       |
| 44 | CdRemetente           | wstr                    | 14                      |
| 45 | DsRemetente           | wstr                    | 100                     |
| 46 | CdDestinatario        | wstr                    | 14                      |
| 47 | DsDestinatario        | wstr                    | 100                     |
| 48 | CdInscricao           | wstr                    | 14                      |
| 49 | DsPagador             | wstr                    | 100                     |
| 50 | CdRedespacho          | wstr                    | 14                      |
| 51 | DsRedespacho          | wstr                    | 40                      |

**Total: 51 colunas**

## Pré-processamento SQL (Task "Truncate fFicha")

```sql
DELETE FROM dbo.fFichaViagem
```

- Conexão: 10.100.86.89.DWGrupolc.sqldba1 (OLEDB / SQLNCLI11.1)
- ThreadHint: 0
- Parâmetro `User::StartDate` vinculado ao índice 0, mas **sem cláusula WHERE** na query
- Efetivamente apaga toda a tabela `fFichaViagem` a cada execução
