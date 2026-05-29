# ETL_Mapeamento_Carga — Origem

## Atributos do Servidor de Origem

| Atributo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | SOFTRAN_TRANSLUTE |
| Usuario | softran |
| Provider | SQLOLEDB.1 |
| ConnectRetryCount | 1 |
| ConnectRetryInterval | 5s |
| ConnectionManager | SOFTRAN_TRANSLUTE |

## Source Component: Origem OLE DB

| Atributo | Valor |
|---|---|
| ComponentClassID | Microsoft.OLEDBSource |
| AccessMode | 2 (SQL Command) |
| CommandTimeout | 0 (sem timeout) |
| DefaultCodePage | 1252 |

### Query SQL

```sql
select * from [dbo].[MapeamentodeCargas] with (nolock)
```

### Colunas de Saida

| # | Coluna | Tipo SSIS | Tamanho | Descricao Semantica |
|---|---|---|---|---|
| 1 | DsApelidoEmitente | DT_STR | 10 | Apelido/sigla da empresa emitente do CTE |
| 2 | DsEmpDest | DT_STR | 10 | Apelido da empresa de destino |
| 3 | nrdoctofiscal | DT_I4 | — | Numero do documento fiscal (CTE) |
| 4 | Previsao de Entrega | DT_STR | 30 | Data prevista de entrega (armazenada como string DD/MM/AAAA) |
| 5 | ValorCTE | DT_CY | — | Valor total do CTE (currency) |
| 6 | Data de Emissao CTE | DT_STR | 30 | Data de emissao do CTE (armazenada como string) |
| 7 | CdRemetente | DT_STR | 14 | CNPJ/CPF do remetente |
| 8 | DsRemetente | DT_STR | 100 | Nome/razao social do remetente |
| 9 | CdDestinatario | DT_STR | 14 | CNPJ/CPF do destinatario |
| 10 | dsDestinatario | DT_STR | 100 | Nome/razao social do destinatario |
| 11 | CdInscricao | DT_STR | 14 | CNPJ/CPF do pagador do frete |
| 12 | dsPagador | DT_STR | 100 | Nome/razao social do pagador |
| 13 | CidadeColeta | DT_STR | 30 | Cidade de origem/coleta |
| 14 | DsUFOrigem | DT_STR | 2 | UF de origem |
| 15 | CidadeEntrega | DT_STR | 30 | Cidade de destino/entrega |
| 16 | DsUFDestino | DT_STR | 2 | UF de destino |
| 17 | Peso | DT_CY | — | Peso da carga (currency/numeric) |
| 18 | Valor Mercadoria | DT_CY | — | Valor declarado da mercadoria |
| 19 | Volume | DT_CY | — | Volume da carga |
| 20 | NrNotaFiscal | DT_STR | 255 | Notas fiscais concatenadas |
| 21 | DsItinerario | DT_STR | 7 | Codigo do itinerario/rota |
| 22 | PlacaManifInterior | DT_STR | 8 | Placa do veiculo no manifesto interior |
| 23 | Motorista Manifesto | DT_STR | 1 | Flag indicador de motorista no manifesto |
| 24 | DtSaidaParceiro | DT_STR | 30 | Data de saida do parceiro (string) |
| 25 | DtSaidachegadadestino | DT_STR | 30 | Data de chegada ao destino (string) |
| 26 | NrRomaneio | DT_STR | 16 | Numero do romaneio de entrega |
| 27 | Motorista romaneio | DT_STR | 1 | Flag indicador de motorista no romaneio |
| 28 | DataRomaneio | DT_STR | 30 | Data do romaneio (string) |
| 29 | DsUltimaOcorrencia | DT_STR | 50 | Descricao da ultima ocorrencia de entrega |
| 30 | DtUltimaOcorrencia | DT_STR | 30 | Data da ultima ocorrencia (string) |

**Total: 30 colunas**

### Saida de Erro

O componente possui saida de erro nao conectada (erro silencioso).
