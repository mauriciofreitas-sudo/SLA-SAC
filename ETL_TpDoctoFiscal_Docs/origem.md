# ETL_TpDoctoFiscal — Origem

## Servidor de Origem

| Propriedade         | Valor                         |
|---------------------|-------------------------------|
| Servidor            | 169.57.181.231                |
| Banco de Dados      | softran_translute             |
| Usuário             | softran                       |
| Tipo de Conexão     | OLEDB (SQLOLEDB.1)            |
| Auto Translate      | False                         |
| ConnectRetryCount   | 1                             |
| ConnectRetryInterval | 5 segundos                  |

> **Observação:** O connection string usa `SQLOLEDB.1` (provider legado), diferente dos outros packages que usam `SQLNCLI11.1` ou ADO.NET. O banco de dados é `softran_translute` (minúsculo), enquanto outros packages usam `SOFTRAN_TRANSLUTE` (maiúsculo) — no SQL Server são equivalentes, mas a inconsistência de naming pode confundir.

## Source Component: Origem OLE DB 1

| Propriedade        | Valor                                    |
|--------------------|------------------------------------------|
| Nome               | Origem OLE DB 1                          |
| AccessMode         | 2 (SQL Command)                          |
| SqlCommand         | `select * from sistdf with(nolock)`      |
| CommandTimeout     | 0 (sem timeout)                          |
| DefaultCodePage    | 1252 (Windows Latin-1 / CP1252)         |
| AlwaysUseDefaultCodePage | false                              |

---

## Query SQL

```sql
select * from sistdf with(nolock)
```

Leitura completa da tabela `sistdf` (tabela de tipos de documento fiscal do SOFTRAN).

---

## Colunas da Saída (Saída de Origem OLE DB)

| # | Coluna           | Tipo SSIS | Tamanho | CodePage | Descrição Semântica                          |
|---|------------------|-----------|---------|----------|----------------------------------------------|
| 1 | CdTpDoctoFiscal  | i4 (int)  | —       | —        | Código identificador do tipo de documento    |
| 2 | DsTpDoctoFiscal  | str       | 40      | 1252     | Descrição completa do tipo de documento      |
| 3 | DsApelido        | str       | 10      | 1252     | Apelido/abreviação do tipo de documento      |
| 4 | InTipoDocumento  | i4 (int)  | —       | —        | Indicador de tipo do documento (flag)        |
| 5 | NrSerie          | str       | 3       | 1252     | Número de série do documento fiscal          |
| 6 | NrModelo         | i4 (int)  | —       | —        | Número do modelo do documento fiscal         |
| 7 | InFormaImpressao | i4 (int)  | —       | —        | Indicador de forma de impressão              |
| 8 | NrSerieERPSen    | str       | 5       | 1252     | Número de série no ERP Sênior                |

> **Observação sobre tipos:** As colunas de texto usam tipo `str` (DT_STR com CodePage 1252) em vez de `wstr` (DT_WSTR Unicode). Isso indica que a tabela `sistdf` usa VARCHAR (não NVARCHAR) e o provider OLEDB mapeia diretamente para str/ansi. A CodePage 1252 é Windows Latin-1, adequada para português.
