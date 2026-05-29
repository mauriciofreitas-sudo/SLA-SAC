# ETL_Romaneios — Origem dos Dados

## Servidor de Origem

| Item            | Valor                                              |
|-----------------|----------------------------------------------------|
| Servidor        | 169.57.181.231                                     |
| Banco           | SOFTRAN_TRANSLUTE                                  |
| Usuário         | softran                                            |
| Connection Name | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc            |
| Tipo            | ADO.NET (System.Data.SqlClient)                    |
| CommandTimeout  | 30 segundos (padrão)                               |

## Source: ADO NET Source (Data Flow "Load fFicha")

- **Componente:** ADO NET Source
- **Modo:** TableOrViewName (AccessMode = 0 — tabela/view direta, sem SQL inline)
- **Objeto de origem:** `"dbo"."Ficha_de_Viagem_Geral"` — view no banco SOFTRAN_TRANSLUTE
- **SqlCommand:** vazio

> Para consultar a definição da view, executar no servidor de origem:
> ```sql
> SELECT OBJECT_DEFINITION(OBJECT_ID('dbo.Ficha_de_Viagem_Geral'));
> ```

## Tabela de Colunas da Origem (ADO NET Source Output)

| # | Nome da Coluna         | Tipo SSIS    | Comprimento / Precisão |
|---|------------------------|--------------|------------------------|
| 1 | EmpresaFicha           | i4           | —                      |
| 2 | NrFicha                | i4           | —                      |
| 3 | DtEmissao              | dbTimeStamp  | —                      |
| 4 | VlComissao             | numeric      | p=14, s=4              |
| 5 | CdRotaRMS              | i4           | —                      |
| 6 | MotoristaFV            | wstr         | 100                    |
| 7 | ProprietarioFV         | wstr         | 100                    |
| 8 | VeiculoFV              | wstr         | 8                      |
| 9 | CarretaFV              | wstr         | 8                      |
| 10 | Carreta2FV            | wstr         | 8                      |
| 11 | Carreta3FV            | wstr         | 8                      |
| 12 | UserFicha             | wstr         | 10                     |
| 13 | EmpresaRomaneio       | i4           | —                      |
| 14 | RotaRomaneio          | i4           | —                      |
| 15 | RomaneioManifesto     | wstr         | 20                     |
| 16 | UserRomaneioManifesto | wstr         | 10                     |
| 17 | DtEmissaoRmMan        | dbTimeStamp  | —                      |
| 18 | VeiculoRmMan          | wstr         | 8                      |
| 19 | CarretaRmMan          | wstr         | 8                      |
| 20 | Carreta2RmMan         | wstr         | 8                      |
| 21 | Carreta3RmMan         | wstr         | 8                      |
| 22 | MotoristaRmMan        | wstr         | 40                     |
| 23 | ProprietarioRmMan     | wstr         | 40                     |
| 24 | NumeroCTRB            | i4           | —                      |
| 25 | UserCtrb              | wstr         | 10                     |
| 26 | DtEmissaoCTRB         | dbTimeStamp  | —                      |
| 27 | ValorTotalCTRB        | numeric      | p=14, s=4              |
| 28 | AdtoCTRB              | numeric      | p=14, s=4              |
| 29 | PedagioCTRB           | numeric      | p=14, s=4              |
| 30 | EmpresaCTE            | i4           | —                      |
| 31 | VlFretePeso           | numeric      | p=14, s=4              |
| 32 | VlFreteValor          | numeric      | p=14, s=4              |
| 33 | VlPedagio             | numeric      | p=14, s=4              |
| 34 | VlGRIS                | numeric      | p=14, s=4              |
| 35 | VlTotalPrestacao      | numeric      | p=14, s=4              |
| 36 | NrDoctoFiscal         | i4           | —                      |
| 37 | UserCTE               | wstr         | 10                     |
| 38 | TipoDoctoFiscal       | wstr         | 10                     |
| 39 | DtEmissaoCTE          | dbTimeStamp  | —                      |
| 40 | CidadeColeta          | wstr         | 30                     |
| 41 | DsUFOrigem            | wstr         | 2                      |
| 42 | CidadeEntrega         | wstr         | 30                     |
| 43 | DsUFDestino           | wstr         | 2                      |
| 44 | CdRemetente           | wstr         | 14                     |
| 45 | DsRemetente           | wstr         | 100                    |
| 46 | CdDestinatario        | wstr         | 14                     |
| 47 | DsDestinatario        | wstr         | 100                    |
| 48 | CdInscricao           | wstr         | 14                     |
| 49 | DsPagador             | wstr         | 100                    |
| 50 | CdRedespacho          | wstr         | 14                     |
| 51 | DsRedespacho          | wstr         | 40                     |

**Total: 51 colunas**
