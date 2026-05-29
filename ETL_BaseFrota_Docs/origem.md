# ETL_BaseFrota — Origem de Dados

## Servidor de Origem

| Campo | Valor |
|---|---|
| Servidor | 169.57.181.231 |
| Banco | SOFTRAN_TRANSLUTE |
| Usuario | softran |
| Connection Manager | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc (ADO.NET SqlClient) |
| CommandTimeout | 600 segundos |

## Data Flow: Load fBaseFrota

### Componente Source

| Campo | Valor |
|---|---|
| Nome | ADO NET Source |
| Tipo | ADO.NET Source (Microsoft.DataReaderSourceAdapter) |
| Objeto/View | `"dbo"."vw_ConsultaVeiculos"` |
| AccessMode | 0 (OpenRowset — leitura direta da view) |
| AllowImplicitStringConversion | true |

### Query SQL Executada

O componente usa AccessMode=0 (leitura direta da view), portanto nao ha SQL customizado — lê toda a view `dbo.vw_ConsultaVeiculos` sem filtros adicionais no SSIS.

> Nota: A query registrada no SqlCommand do componente é a mesma utilizada para pré-visualização de metadados (SELECT com multiplos JOINs partindo de `gtcconhe`), mas no modo AccessMode=0 a execucao real é via OpenRowset na view.

### Colunas de Saida (ADO NET Source Output)

| # | Coluna | Tipo SSIS | Tamanho | Observacao |
|---|---|---|---|---|
| 1 | NrPlaca | wstr | 8 | Placa do veiculo |
| 2 | CdFabricRastreador | i4 | — | Codigo fabricante rastreador |
| 3 | DsFabricante | wstr | 40 | Descricao do fabricante |
| 4 | NumeroRastreador | i4 | — | Numero do rastreador |
| 5 | NrAnoFabricacao | i4 | — | Ano de fabricacao |
| 6 | DsCores | wstr | 40 | Cor do veiculo |
| 7 | NrChassis | wstr | 25 | Numero do chassi |
| 8 | DtCadastro | dbTimeStamp | — | Data de cadastro |
| 9 | DsUsuarioInc | wstr | 10 | Usuario que incluiu |
| 10 | DtAtualizacao | dbTimeStamp | — | Data da ultima atualizacao |
| 11 | DsUsuAlteracao | wstr | 10 | Usuario que alterou |
| 12 | NrRenavan | wstr | 15 | Numero RENAVAN |
| 13 | DsObservacao | wstr | 500 | Observacoes gerais |
| 14 | Tipo | wstr | 8 | Tipo do veiculo (ex: Cavalo, Carreta) |
| 15 | Proprietario | wstr | 60 | Nome do proprietario |
| 16 | DsTpVeiculo | wstr | 40 | Descricao do tipo de veiculo |
| 17 | CdEmpresa | i4 | — | Codigo da empresa |
| 18 | Situacao | wstr | 7 | Situacao do veiculo (Ativo/Inativo) |
| 19 | VeicAnterior | wstr | 8 | Placa do veiculo anterior |
| 20 | CdANTT | wstr | 20 | Codigo ANTT |

**Total de colunas: 20**

### Tratamento de Erros na Origem

- errorRowDisposition: FailComponent (falha no componente em caso de erro)
- truncationRowDisposition: FailComponent (falha no componente em caso de truncamento)
- Error Output desconectado (ADO NET Source Error Output nao esta mapeado para nenhum destino)
