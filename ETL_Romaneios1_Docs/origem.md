# ETL_Romaneios1 — Origem

## Servidor de Origem

| Propriedade         | Valor                        |
|---------------------|------------------------------|
| Servidor            | 169.57.181.231               |
| Banco de Dados      | SOFTRAN_TRANSLUTE            |
| Usuário             | softran                      |
| Tipo de Conexão     | ADO.NET (SqlClient)          |
| TrustServerCertificate | True                      |

## Source Component: ADO NET Source

| Propriedade          | Valor                                  |
|----------------------|----------------------------------------|
| Nome do Componente   | ADO NET Source                         |
| Objeto/View          | `"dbo"."Ficha_de_Viagem_Geral"`        |
| AccessMode           | 0 (Table or View)                      |
| SqlCommand           | (vazio — usa TableOrViewName)          |
| CommandTimeout       | 600 segundos                           |
| AllowImplicitStringConversion | true                        |
| ValidateExternalMetadata | False                              |

> **Nota:** AccessMode=0 significa que a leitura é feita diretamente da view `dbo.Ficha_de_Viagem_Geral` sem SQL customizado. O filtro de data não é aplicado na origem via SQL da task — ele depende da definição interna da view ou de lógica adicional não visível neste package.

---

## Colunas da Saída (ADO NET Source Output)

| # | Coluna                   | Tipo SSIS      | Tamanho / Precisão        | Descrição Semântica                                  |
|---|--------------------------|----------------|---------------------------|------------------------------------------------------|
| 1  | EmpresaFicha            | i4             | —                         | Código da empresa emissora da ficha de viagem        |
| 2  | NrFicha                 | i4             | —                         | Número da ficha de viagem                            |
| 3  | DtEmissao               | dbTimeStamp    | —                         | Data de emissão da ficha                             |
| 4  | VlComissao              | numeric        | p14, s4                   | Valor de comissão da ficha                           |
| 5  | CdRotaRMS               | i4             | —                         | Código da rota RMS                                   |
| 6  | MotoristaFV             | wstr           | 100                       | Nome do motorista na ficha de viagem                 |
| 7  | ProprietarioFV          | wstr           | 100                       | Proprietário do veículo na ficha de viagem           |
| 8  | VeiculoFV               | wstr           | 8                         | Placa do veículo principal na ficha de viagem        |
| 9  | CarretaFV               | wstr           | 8                         | Placa da carreta 1 na ficha de viagem                |
| 10 | Carreta2FV              | wstr           | 8                         | Placa da carreta 2 na ficha de viagem                |
| 11 | Carreta3FV              | wstr           | 8                         | Placa da carreta 3 na ficha de viagem                |
| 12 | UserFicha               | wstr           | 10                         | Usuário responsável pela ficha                       |
| 13 | EmpresaRomaneio         | i4             | —                         | Código da empresa do romaneio                        |
| 14 | RotaRomaneio            | i4             | —                         | Código da rota do romaneio                           |
| 15 | RomaneioManifesto       | wstr           | 20                        | Número do romaneio / manifesto                       |
| 16 | UserRomaneioManifesto   | wstr           | 10                        | Usuário do romaneio/manifesto                        |
| 17 | DtEmissaoRmMan          | dbTimeStamp    | —                         | Data de emissão do romaneio/manifesto                |
| 18 | VeiculoRmMan            | wstr           | 8                         | Placa do veículo no romaneio/manifesto               |
| 19 | CarretaRmMan            | wstr           | 8                         | Placa da carreta 1 no romaneio/manifesto             |
| 20 | Carreta2RmMan           | wstr           | 8                         | Placa da carreta 2 no romaneio/manifesto             |
| 21 | Carreta3RmMan           | wstr           | 8                         | Placa da carreta 3 no romaneio/manifesto             |
| 22 | MotoristaRmMan          | wstr           | 40                        | Motorista no romaneio/manifesto                      |
| 23 | ProprietarioRmMan       | wstr           | 40                        | Proprietário no romaneio/manifesto                   |
| 24 | NumeroCTRB              | i4             | —                         | Número do CTe/RB (conhecimento de transporte)        |
| 25 | UserCtrb                | wstr           | 10                        | Usuário do CTe/RB                                    |
| 26 | DtEmissaoCTRB           | dbTimeStamp    | —                         | Data de emissão do CTe/RB                            |
| 27 | ValorTotalCTRB          | numeric        | p14, s4                   | Valor total do CTe/RB                                |
| 28 | AdtoCTRB                | numeric        | p14, s4                   | Adiantamento do CTe/RB                               |
| 29 | PedagioCTRB             | numeric        | p14, s4                   | Pedágio do CTe/RB                                    |
| 30 | EmpresaCTE              | i4             | —                         | Código da empresa do CTe                             |
| 31 | VlFretePeso             | numeric        | p14, s4                   | Valor do frete peso                                  |
| 32 | VlFreteValor            | numeric        | p14, s4                   | Valor do frete valor                                 |
| 33 | VlPedagio               | numeric        | p14, s4                   | Valor do pedágio                                     |
| 34 | VlGRIS                  | numeric        | p14, s4                   | Valor do GRIS (gerenciamento de risco)               |
| 35 | VlTotalPrestacao        | numeric        | p14, s4                   | Valor total da prestação de serviço                  |
| 36 | NrDoctoFiscal           | i4             | —                         | Número do documento fiscal                           |
| 37 | UserCTE                 | wstr           | 10                        | Usuário do CTe                                       |
| 38 | TipoDoctoFiscal         | wstr           | 10                        | Tipo do documento fiscal                             |
| 39 | DtEmissaoCTE            | dbTimeStamp    | —                         | Data de emissão do CTe                               |
| 40 | CidadeColeta            | wstr           | 30                        | Cidade de coleta (origem)                            |
| 41 | DsUFOrigem              | wstr           | 2                         | UF de origem                                         |
| 42 | CidadeEntrega           | wstr           | 30                        | Cidade de entrega (destino)                          |
| 43 | DsUFDestino             | wstr           | 2                         | UF de destino                                        |
| 44 | CdRemetente             | wstr           | 14                        | CNPJ/CPF do remetente                                |
| 45 | DsRemetente             | wstr           | 100                       | Nome do remetente                                    |
| 46 | CdDestinatario          | wstr           | 14                        | CNPJ/CPF do destinatário                             |
| 47 | DsDestinatario          | wstr           | 100                       | Nome do destinatário                                 |
| 48 | CdInscricao             | wstr           | 14                        | CNPJ/CPF do pagador                                  |
| 49 | DsPagador               | wstr           | 100                       | Nome do pagador do frete                             |
| 50 | CdRedespacho            | wstr           | 14                        | CNPJ/CPF do redespacho                               |
| 51 | DsRedespacho            | wstr           | 40                        | Nome do redespacho                                   |

> **Nota:** A Destination possui colunas adicionais no ExternalMetadata (NrDiasAtraso, Status, DtPrevisaoEntregaCTeNova, FichaViagem, Hora, IDCTEPERF, CNPJRemetente, NomeRemetente, NFSerie, NFNr, NFVlr, NFDtEmissao, CTeEmpresa, CTeNr, CTeDtEmissao, DtPrevisaoEntregaCTe, DtAgendamentoEntregaCTe, CTeDtEntrega, CNPJDestinatario, NomeDestinatario, CidadeDestino, UFDestino, CNPJPagador, NomePagador, NrSeqControle, NaturezaCod, NaturezaNome, TipoTransporte, Volumes, QtPesoCubado, HistEntregaCod, HistEntregaNome, FichaVUltManifesto, FichaVUltRom, DtRomaneio, DtManifesto, TipoEmissao, DsGrupoCliente, CdGrupoCliente, NrRegistros) que existem na tabela destino mas NÃO são mapeadas pelo pipeline — a origem não as fornece.
