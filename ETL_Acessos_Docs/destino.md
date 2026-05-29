# Destino dos Dados — ETL_Acessos

## Identificação do Servidor de Destino

| Propriedade | Valor |
|-------------|-------|
| Servidor | 10.100.86.89 |
| Banco de Dados | DWGrupolc |
| Usuário | datalc |
| Tipo de Conexão | ADO.NET — System.Data.SqlClient |
| Connection Manager | 10.100.86.89.DWGrupolc.datalc |
| TrustServerCertificate | True |

## Componente Destination

### ADO NET Destination — `fAcessos_Softran`

| Propriedade | Valor |
|-------------|-------|
| Tabela destino | `"fAcessos_Softran"` |
| BatchSize | 0 (usa tamanho do buffer SSIS) |
| CommandTimeout | 30 segundos |
| UseBulkInsertWhenPossible | true |
| Estratégia de carga | **Append** — o package não executa DELETE/TRUNCATE antes da carga |

### Colunas no Destino

| # | Coluna | Tipo SSIS | Tamanho | Origem |
|---|--------|-----------|---------|--------|
| 1 | Acesso | i4 (Int32) | — | ADO NET Source → Acesso |
| 2 | DsUsuario | wstr | 10 | ADO NET Source → DsUsuario |
| 3 | CdModulo | wstr | 3 | ADO NET Source → CdModulo |
| 4 | UltimoAcesso | dbTimeStamp (DateTime) | — | ADO NET Source → UltimoAcesso |
| 5 | Logon | dbTimeStamp (DateTime) | — | ADO NET Source → Logon |
| 6 | ExecutionDt | date | — | Derived Column → @[User::ExecutionDateTime] |

> Nota: A coluna `ExecutionDt` no destino é mapeada como tipo `dbTimeStamp` nos metadados externos, mas a Derived Column a produz como `date`. Existe um mismatch de tipo que pode gerar truncamento silencioso na parte de hora.

### Configurações de Erro do Destino

| Saída | Comportamento |
|-------|---------------|
| errorRowDisposition | FailComponent |
| Error Output | Disponível (ErrorCode + ErrorColumn) mas não conectado |

## Conexão Adicional (não utilizada no fluxo)

| Connection Manager | Tipo | Observação |
|-------------------|------|------------|
| 10.100.86.89.DWGrupolc.sqldba1 | OLEDB / SQLNCLI11.1 | Conexão declarada no package mas não referenciada por nenhum componente do Data Flow |
