# ETL_TpDoctoFiscal — Destino

## Servidor de Destino

| Propriedade        | Valor                          |
|--------------------|--------------------------------|
| Servidor           | 10.100.86.89                   |
| Banco de Dados     | DBStage                        |
| Usuário            | sqldba                         |
| Tipo de Conexão    | OLEDB (SQLOLEDB.1)             |
| Auto Translate     | False                          |
| ConnectRetryCount  | 1                              |
| ConnectRetryInterval | 5 segundos                   |

---

## Destination Component: Destino OLE DB 1

| Propriedade             | Valor                        |
|-------------------------|------------------------------|
| Nome                    | Destino OLE DB 1             |
| OpenRowset              | `[dbo].[tb_SERIE]`           |
| AccessMode              | 3 (FastLoad)                 |
| CommandTimeout          | 0 (sem timeout)              |
| FastLoadKeepIdentity    | false                        |
| FastLoadKeepNulls       | false                        |
| FastLoadOptions         | `TABLOCK,CHECK_CONSTRAINTS`  |
| FastLoadMaxInsertCommitSize | 2147483647 (confirma no final) |
| DefaultCodePage         | 1252                         |

---

## Estratégia de Carga

**DELETE + INSERT com FastLoad (TABLOCK)**

1. Task `Tarefa Executar SQL` executa `DELETE FROM [dbo].[tb_SERIE]` via DBStage
2. `Tarefa Fluxo de Dados` insere usando OLE DB FastLoad com `TABLOCK,CHECK_CONSTRAINTS`

O uso de `TABLOCK` bloqueia a tabela durante a inserção, o que é eficiente para tabelas pequenas de referência (catálogos).

---

## Schema da Tabela Destino `[dbo].[tb_SERIE]`

| # | Coluna           | Tipo SSIS | Tamanho | CodePage | Tipo SQL (inferido) | Nulável |
|---|------------------|-----------|---------|----------|---------------------|---------|
| 1 | CdTpDoctoFiscal  | i4        | —       | —        | INT                 | Sim     |
| 2 | DsTpDoctoFiscal  | str       | 40      | 1252     | VARCHAR(40)         | Sim     |
| 3 | DsApelido        | str       | 10      | 1252     | VARCHAR(10)         | Sim     |
| 4 | InTipoDocumento  | i4        | —       | —        | INT                 | Sim     |
| 5 | NrSerie          | str       | 3       | 1252     | VARCHAR(3)          | Sim     |
| 6 | NrModelo         | i4        | —       | —        | INT                 | Sim     |
| 7 | InFormaImpressao | i4        | —       | —        | INT                 | Sim     |

**Colunas Mapeadas: 7**

> **Observação:** A coluna `NrSerieERPSen` existe no external metadata do Destination como coluna adicional da tabela (str, 5, cp1252), mas **não está mapeada** no pipeline — a origem a envia mas não há `inputColumn` para ela na Destination. Isso significa que `NrSerieERPSen` sempre ficará NULL na tabela destino após a carga.

---

## Coluna Não Mapeada

| Coluna        | Tipo na Origem | Tipo na Tabela (ext) | Situação        |
|---------------|----------------|----------------------|-----------------|
| NrSerieERPSen | str, 5, cp1252 | str, 5, cp1252       | Não mapeada — sempre NULL após a carga |
