# Origem dos Dados — ETL_Acessos

## Identificação do Servidor de Origem

| Propriedade | Valor |
|-------------|-------|
| Servidor | 169.57.181.231 |
| Banco de Dados | SOFTRAN_TRANSLUTE |
| Usuário | softran |
| Tipo de Conexão | ADO.NET — System.Data.SqlClient |
| Connection Manager | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc |
| TrustServerCertificate | True |

## Componente Source

### ADO NET Source — `vw_acesso_softran`

| Propriedade | Valor |
|-------------|-------|
| Objeto de origem | `"dbo"."vw_acesso_softran"` |
| Modo de acesso | AccessMode = 0 (TableOrViewName) |
| SqlCommand | *(vazio — leitura direta da view)* |
| CommandTimeout | 600 segundos |
| AllowImplicitStringConversion | true |
| validateExternalMetadata | False |

### Colunas Extraídas da Source

| # | Coluna | Tipo SSIS | Tamanho | Descrição |
|---|--------|-----------|---------|-----------|
| 1 | Acesso | i4 (Int32) | — | Identificador numérico do acesso |
| 2 | DsUsuario | wstr | 10 | Código/descrição do usuário |
| 3 | CdModulo | wstr | 3 | Código do módulo acessado |
| 4 | UltimoAcesso | dbTimeStamp (DateTime) | — | Data/hora do último acesso |
| 5 | Logon | dbTimeStamp (DateTime) | — | Data/hora do logon |

### Configurações de Erro da Source

| Saída | Comportamento |
|-------|---------------|
| errorRowDisposition | FailComponent |
| truncationRowDisposition | FailComponent |
| Error Output | Disponível mas não conectado a nenhum destino de erro |
