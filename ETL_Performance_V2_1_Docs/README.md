# ETL_Performance_V2 (1) — Documentação do Package SSIS

## Metadados

| Campo                        | Valor                                      |
|------------------------------|--------------------------------------------|
| ObjectName                   | ETL_Performance                            |
| Arquivo Físico               | ETL_Performance_V2 (1).dtsx               |
| CreationDate                 | 5/28/2025 2:15:09 PM                       |
| CreatorName                  | GRUPOLCLOG\luiz.costa                      |
| CreatorComputerName          | D2-WV47-DB01                               |
| LastModifiedProductVersion   | 16.0.5685.0 (SQL Server 2022)              |
| VersionBuild                 | **106** (vs. 95 no V2 original)            |
| VersionGUID                  | {18CB645A-2ADE-444E-95F8-7FD3F2F3C1F5}    |
| DTSID                        | {F5979936-9A58-4610-9079-3BA020F8F4C4}    |
| ProtectionLevel              | 0                                          |
| PackageFormatVersion         | 8                                          |

## Objetivo

Mesmo objetivo do `ETL_Performance_V2.dtsx`: carga da tabela `dbo.fPerformance` no banco `DBStage`.

Este arquivo é uma versão mais recente (VersionBuild 106 vs. 95) com a **diferença crítica na lógica de limpeza da tabela**.

## Diagrama ASCII — Fluxo de Controle

```
+----------------+
| GetDate 1M     |  Execute SQL Task — DISABLED
| (Desabilitado) |  SELECT DATEADD(month, ...) → User::StartDate
+----------------+
         |
         | (Success — desabilitado, não executa)
         v
+------------------+
|  Truncate fPerf  |  Execute SQL Task
|                  |  DELETE com WHILE + filtro por data
+------------------+
         |
         | (Success)
         v
+------------------+
|   Load fPerf     |  Data Flow Task (idêntico ao V2)
+------------------+
```

## Diferenças em Relação ao ETL_Performance_V2.dtsx

| Item                          | ETL_Performance_V2.dtsx            | ETL_Performance_V2 (1).dtsx                      |
|-------------------------------|-------------------------------------|--------------------------------------------------|
| VersionBuild                  | 95                                  | **106**                                          |
| VersionGUID                   | {29B01A45-1FCA-4CCA-BCC7-...}       | **{18CB645A-2ADE-444E-95F8-...}**               |
| DTSID                         | {F0A08D3A-6D3C-4FBA-9906-...}       | **{F5979936-9A58-4610-9079-...}**               |
| SQL da task "Truncate fPerf"  | `DELETE FROM dbo.fPerformance` (sem WHERE — apaga tudo) | `WHILE 1=1 BEGIN DELETE FROM dbo.fPerformance WHERE NFDTEmissao >= DATEADD(MONTH,-1,GETDATE()); IF @@ROWCOUNT=0 BREAK; END` (com filtro de 1 mês) |
| Conexão DBStage.sqldba1 OLEDB | Provider=SQLNCLI11.1                | **Provider=MSOLEDBSQL** (driver atualizado)      |
| Conexão DWGrupolc.sqldba1 OLEDB | Provider=SQLNCLI11.1              | **Provider=MSOLEDBSQL** (driver atualizado)      |
| Conexão SOFTRAN.softran OLEDB | Provider=SQLNCLI11.1                | **Provider=MSOLEDBSQL** (driver atualizado)      |
| Senha DBStage.sqldba 1        | Criptografada (diferente)           | Criptografada (diferente) — novos GUIDs          |
| Lógica de carga (Data Flow)   | Idêntica (43 colunas, vw_fPerformance) | Idêntica (43 colunas, vw_fPerformance)         |
| Colunas mapeadas              | 43 (idêntico)                       | 43 (idêntico)                                    |
| Variável StartDate            | 1/1/2025 (padrão)                   | 1/1/2025 (padrão)                                |

## Melhoria Principal da Versão (1)

A versão (1) **corrige parcialmente** o issue crítico #1 do V2 original:

**V2 original (bug):**
```sql
DELETE FROM dbo.fPerformance
```

**V2 (1) — versão corrigida:**
```sql
WHILE 1 = 1
BEGIN
    DELETE FROM dbo.fPerformance
    WHERE NFDTEmissao >= DATEADD(MONTH, -1, GETDATE());

    IF @@ROWCOUNT = 0
        BREAK;
END
```

Esta versão usa um loop WHILE para deletar em lotes registros do último mês, evitando locks longos em tabelas grandes.

**Importante:** Há uma inconsistência de nomenclatura — a coluna no filtro é `NFDTEmissao` (maiúsculas `DT`) enquanto no mapeamento do Data Flow a coluna correspondente chama-se `NFDtEmissao` (minúsculas `t`). Ver issues.

## Tabela de Conexões

| Nome                                     | Tipo    | Servidor       | Banco              | Usuário  | Observação                          |
|------------------------------------------|---------|----------------|--------------------|----------|-------------------------------------|
| 10.100.86.89.DBStage.sqldba              | ADO.NET | 10.100.86.89   | DBStage            | sqldba   | Destino — GetDate + Delete          |
| 10.100.86.89.DBStage.sqldba 1            | ADO.NET | 10.100.86.89   | DBStage            | sqldba   | Destino — escrita fPerformance      |
| 10.100.86.89.DBStage.sqldba1             | OLEDB   | 10.100.86.89   | DBStage            | sqldba   | **MSOLEDBSQL** (atualizado vs. V2)  |
| 10.100.86.89.DWGrupolc.datalc            | ADO.NET | 10.100.86.89   | DWGrupolc          | sqldba   | Declarada, não utilizada            |
| 10.100.86.89.DWGrupolc.sqldba1           | OLEDB   | 10.100.86.89   | DWGrupolc          | sqldba   | **MSOLEDBSQL** (atualizado vs. V2)  |
| 169.57.181.231.SOFTRAN_TRANSLUTE.datalc  | ADO.NET | 169.57.181.231 | SOFTRAN_TRANSLUTE  | softran  | Origem — leitura vw_fPerformance    |
| 169.57.181.231.SOFTRAN_TRANSLUTE.softran | OLEDB   | 169.57.181.231 | SOFTRAN_TRANSLUTE  | softran  | **MSOLEDBSQL** (atualizado vs. V2)  |

## Variáveis

| Nome      | Namespace | Tipo     | Valor Padrão | Observação                |
|-----------|-----------|----------|--------------|---------------------------|
| StartDate | User      | DateTime | 1/1/2025     | Vinculada ao DELETE mas sem uso efetivo (task GetDate desabilitada) |

## Precedence Constraints (dentro do Sequence Container)

| De             | Para           | Condição |
|----------------|----------------|----------|
| GetDate 1M     | Truncate fPerf | Success  |
| Truncate fPerf | Load fPerf     | Success  |
