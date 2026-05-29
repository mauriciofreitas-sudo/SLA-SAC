# ETL_TpDoctoFiscal — Issues e Observações

## Tabela Resumo de Severidades

| Severidade | Quantidade | Descrição Geral                                |
|------------|------------|------------------------------------------------|
| ALTA       | 2          | Problemas com perda de dados ou segurança      |
| MEDIA      | 3          | Problemas de design e consistência             |
| BAIXA      | 3          | Melhorias de boas práticas                     |

---

## Issues Encontrados

### [ALTA-01] Coluna `NrSerieERPSen` não mapeada — dados perdidos

**Localização:** `Tarefa Fluxo de Dados` — Destino OLE DB 1

A coluna `NrSerieERPSen` (str, 5, cp1252) existe em:
- Na tabela de origem `sistdf` (mapeada na Saída de Origem OLE DB)
- No external metadata da tabela destino `tb_SERIE` (coluna existe no banco)

Porém **não há `inputColumn`** para ela no Destination. Isso significa que a coluna `NrSerieERPSen` sempre receberá NULL na tabela `tb_SERIE` após cada carga, mesmo havendo dados válidos na origem.

**Sugestão:** Adicionar o mapeamento de `NrSerieERPSen` no Destination para completar a carga.

---

### [ALTA-02] Error Output do Destination desconectado

**Localização:** `Destino OLE DB 1`

O Error Output (`Saída de Erro de Destino OLE DB`) não está conectado a nenhum destino. Erros de linha causarão falha total do package sem rastreabilidade.

**Sugestão:** Conectar o Error Output a uma tabela de log ou arquivo de erro.

---

### [MEDIA-01] Nome interno do package diverge do arquivo e do application name

| Propriedade       | Valor                        |
|-------------------|------------------------------|
| DTS:ObjectName    | SSIS_stg_TpDoctoFiscal       |
| Nome do arquivo   | ETL_TpDoctoFiscal.dtsx       |
| Application Name na connection string | SSIS-SSIS_stg_DsTransporte-... |

O `Application Name` na connection string do DBStage ainda referencia `SSIS_stg_DsTransporte`, indicando que este package foi copiado de outro (`SSIS_stg_DsTransporte`) sem atualizar todos os atributos. Isso dificulta rastreamento nas logs do SQL Server (`sys.dm_exec_sessions`).

**Sugestão:** Regenerar os connection managers ou atualizar o Application Name para refletir o nome correto do package.

---

### [MEDIA-02] Provider OLEDB legado (SQLOLEDB.1)

**Localização:** Ambas as connection managers

O provider `SQLOLEDB.1` é um provider legado. O recomendado para SQL Server moderno é `SQLNCLI11.1` ou `MSOLEDBSQL`.

**Impacto:** Possível incompatibilidade com versões futuras do SQL Server ou com features de segurança modernas (TLS 1.2 pode não ser garantido com SQLOLEDB.1).

**Sugestão:** Migrar para `MSOLEDBSQL` (Microsoft OLE DB Driver for SQL Server).

---

### [MEDIA-03] DELETE sem WHERE — apaga tudo, sem atomicidade

**Localização:** `Tarefa Executar SQL`
```sql
DELETE FROM [dbo].[tb_SERIE]
```

Tabela de referência (catálogo) apagada completamente antes do reload. Se o Data Flow falhar, a tabela fica vazia.

**Sugestão:** Para tabelas de referência pequenas, usar TRUNCATE + INSERT em uma transação explícita, ou usar MERGE para upsert incremental.

---

### [BAIXA-01] Falta de timeout na origem (CommandTimeout = 0)

A Origem OLE DB usa `CommandTimeout = 0` (infinito). Embora adequado para algumas situações, pode manter uma conexão aberta indefinidamente se a query travar.

---

### [BAIXA-02] Tabela destino em banco DBStage, não no DWGrupolc

Este package carrega em `DBStage` (banco de staging), diferentemente dos outros packages que carregam em `DWGrupolc`. Isso sugere uma arquitetura de dois níveis (staging → DW), mas não há evidência de uma segunda etapa de carga do staging para o DW neste package.

**Sugestão:** Documentar se existe um pipeline downstream que lê `DBStage.dbo.tb_SERIE` e carrega no DWGrupolc.

---

### [BAIXA-03] Sem auditoria de carga

Não há registro de timestamp, total de linhas ou status.

---

## Checklist de Melhoria

- [ ] Adicionar mapeamento de `NrSerieERPSen` no Destination
- [ ] Conectar Error Output a destino de log
- [ ] Corrigir Application Name na connection string do DBStage
- [ ] Migrar provider OLEDB de `SQLOLEDB.1` para `MSOLEDBSQL`
- [ ] Avaliar uso de TRUNCATE em vez de DELETE (mais rápido para tabelas de catálogo)
- [ ] Documentar se existe etapa downstream de DBStage → DWGrupolc
- [ ] Adicionar auditoria de carga
- [ ] Alinhar nome interno do package com nome do arquivo
