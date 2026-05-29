# ETL_Emissao_CTRB — Issues e Observacoes

## Tabela de Severidades

| Simbolo | Nivel | Descricao |
|---|---|---|
| 🔴 | Critico | Pode causar falha em producao ou perda de dados |
| 🟠 | Alto | Risco operacional significativo ou dado incorreto |
| 🟡 | Medio | Problema de qualidade ou manutencao |
| 🔵 | Baixo | Sugestao de melhoria ou padrao |

## Issues Encontrados

### 🔴 ISSUE-01: Sequence Container inteiro DESABILITADO

| Campo | Detalhe |
|---|---|
| Componentes | Delete Table + Tarefa Fluxo de Dados |
| Problema | Ambas as tasks dentro do `Contêiner da Sequência` estao com `DTS:Disabled="True"`. Isso significa que o package nao executa nenhum DELETE nem carregamento dos dados definidos neste container |
| Risco | Se for esperado que este container execute, o package esta silenciosamente pulando toda a logica de carga. Pode haver outro mecanismo de carga externo nao documentado |
| Recomendacao | Esclarecer se o container deve estar habilitado. Se o fluxo ativo for o correto, remover o container desabilitado para evitar confusao |

### 🔴 ISSUE-02: DELETE sem WHERE

| Campo | Detalhe |
|---|---|
| Task | Delete Table (DESABILITADA) |
| SQL | `delete FROM [dbo].[stg_Base_Emissão_CTRB]` |
| Problema | DELETE sem clausula WHERE apaga TODOS os registros da tabela staging sem nenhum filtro |
| Risco | Em caso de reabilitacao acidental da task, todos os dados sao apagados |
| Recomendacao | Substituir por `TRUNCATE TABLE` (mais performatico) ou adicionar filtro de data se carga for incremental |

### 🟠 ISSUE-03: Datas armazenadas como strings

| Campo | Detalhe |
|---|---|
| Colunas | DtInclusao (str,30), DtSaida (str,30), DtCTRB (str,30) |
| Problema | Tres colunas de data sao transportadas como strings de 30 caracteres ao inves de dbTimeStamp |
| Risco | Impossivel fazer filtros, ordenacoes ou calculos de intervalo diretamente; risco de inconsistencia de formato (DD/MM/YYYY vs MM/DD/YYYY) |
| Recomendacao | Converter para dbTimeStamp/datetime na view de origem ou usar Data Conversion no SSIS |

### 🟠 ISSUE-04: Escala de precisao inconsistente em colunas monetarias

| Campo | Detalhe |
|---|---|
| Colunas | VlFreteCTRB, VlAdiantamento, VlPedagioCTRB |
| Problema | Na origem chegam com scale=13, mas nos metadados de destino estao registradas como scale=4. Ha discrepancia entre cached e externalMetadata |
| Risco | Arredondamento de valores financeiros; perda de centesimos em calculos |
| Recomendacao | Alinhar a escala e validar se ha perda de precisao nos registros existentes |

### 🟠 ISSUE-05: Excesso de connection managers (8 conexoes para 2 servidores)

| Campo | Detalhe |
|---|---|
| Conexoes | 8 connection managers definidas para apenas 2 servidores distintos |
| Problema | Multiplas conexoes para o mesmo servidor com diferentes providers (SQLOLEDB.1, MSOLEDBSQL.1, ODBC, ADO.NET). SQLOLEDB.1 e um driver legado e descontinuado |
| Risco | Comportamento inconsistente entre providers; dificuldade de manutencao |
| Recomendacao | Consolidar em 2 conexoes (1 por servidor), usando MSOLEDBSQL ou ADO.NET SqlClient |

### 🟡 ISSUE-06: Nome interno do package diferente do nome do arquivo

| Campo | Detalhe |
|---|---|
| ObjectName interno | `SSIS_stg_Emissao_CTRB_Atual` |
| Nome do arquivo | `ETL_Emissao_CTRB.dtsx` |
| Problema | O nome interno e o nome do arquivo sao diferentes, o que pode causar confusao ao referenciar o package em catálogos SSIS ou SQL Agent |
| Recomendacao | Alinhar o ObjectName interno com o nome do arquivo |

### 🟡 ISSUE-07: Nome de coluna semanticamente incorreto

| Campo | Detalhe |
|---|---|
| Coluna | DsLacre (tipo i4/inteiro) |
| Problema | O prefixo `Ds` (descricao) e usado para uma coluna inteira, que provavelmente e um codigo ou numero do lacre |
| Recomendacao | Renomear para `NrLacre` ou `CdLacre` para seguir o padrao de nomenclatura do restante do schema |

### 🟡 ISSUE-08: Error Output com RedirectRow no destino

| Campo | Detalhe |
|---|---|
| Componente | OLE DB Destination |
| errorRowDisposition | RedirectRow |
| Problema | Linhas com erro sao redirecionadas mas o output de erro nao esta conectado a nenhum destino |
| Risco | Linhas rejeitadas sao silenciosamente descartadas |
| Recomendacao | Conectar o error output a uma tabela de rejeicoes ou arquivo de log |

## Checklist de Melhorias

- [ ] Esclarecer e documentar por que o Sequence Container esta desabilitado
- [ ] Substituir DELETE sem WHERE por TRUNCATE ou DELETE com filtro de data
- [ ] Converter DtInclusao, DtSaida e DtCTRB de string para datetime na origem
- [ ] Alinhar escala de VlFreteCTRB, VlAdiantamento e VlPedagioCTRB (s13 vs s4)
- [ ] Consolidar as 8 connection managers em 2 (uma por servidor)
- [ ] Atualizar provider de SQLOLEDB.1 (legado) para MSOLEDBSQL
- [ ] Sincronizar nome interno do package com nome do arquivo
- [ ] Renomear DsLacre para NrLacre ou CdLacre
- [ ] Conectar error output do destino a tabela de rejeicoes
- [ ] Adicionar auditoria de execucao (data, linhas carregadas)
