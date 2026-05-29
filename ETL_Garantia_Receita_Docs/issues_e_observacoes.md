# ETL_Garantia_Receita — Issues e Observacoes

## Tabela de Severidades

| Simbolo | Nivel | Descricao |
|---|---|---|
| 🔴 | Critico | Pode causar falha em producao ou perda de dados |
| 🟠 | Alto | Risco operacional significativo ou dado incorreto |
| 🟡 | Medio | Problema de qualidade ou manutencao |
| 🔵 | Baixo | Sugestao de melhoria ou padrao |

## Issues Encontrados

### 🔴 ISSUE-01: DELETE sem WHERE

| Campo | Detalhe |
|---|---|
| Task | Delete Table |
| SQL | `delete from [dbo].[GARANTIA_RECEITA]` |
| Problema | DELETE sem clausula WHERE apaga TODOS os registros sem filtro de data ou chave |
| Risco | Em caso de falha na carga subsequente, a tabela ficara vazia em producao |
| Recomendacao | Substituir por `TRUNCATE TABLE [dbo].[GARANTIA_RECEITA]` (mais rapido, usa menos log) ou adicionar logica de carga incremental com DELETE por periodo |

### 🟠 ISSUE-02: Typo no nome da Connection Manager

| Campo | Detalhe |
|---|---|
| Connection Manager | SOFTRANS - TRANSLUTE (com S) |
| Esperado | SOFTRAN - TRANSLUTE (sem S) — padrao dos outros packages |
| Problema | O nome difere de todos os outros packages que usam o mesmo servidor (ETL_Emissao_CTRB usa "SOFTRAN - TRANSLUTE") |
| Risco | Confusao ao referenciar a conexao; possivel problema ao mover para ambiente centralizado |
| Recomendacao | Renomear para "SOFTRAN - TRANSLUTE" (padrao) |

### 🟠 ISSUE-03: Inconsistencia de precisao/escala em GRIS e Pedagio

| Campo | Detalhe |
|---|---|
| Colunas | GRIS, Pedágio |
| Origem | numeric p14,s4 |
| Destino (externalMetadata) | numeric p15,s2 |
| Problema | A precisao e escala diferem entre origem e destino, o que pode causar arredondamento silencioso |
| Risco | Valores financeiros arredondados sem alerta |
| Recomendacao | Alinhar o tipo da coluna de destino na tabela GARANTIA_RECEITA para p14,s4 |

### 🟠 ISSUE-04: Tamanho inconsistente em CTe Substituido

| Campo | Detalhe |
|---|---|
| Coluna | CTe Substituido |
| cached tamanho | 3 (no destino) |
| external tamanho | 5 (schema real da tabela) |
| Problema | O SSIS registra o tamanho cached como 3, mas a coluna real tem 5. Possivel truncamento |
| Risco | Dados truncados ao gravar valores com mais de 3 caracteres |
| Recomendacao | Atualizar metadados do componente destino |

### 🟡 ISSUE-05: Colunas UF com tamanho 3 ao inves de 2

| Campo | Detalhe |
|---|---|
| Colunas | Origem UF (str,3), Destino UF (str,3) |
| Problema | UF brasileira tem 2 caracteres. Tamanho 3 sugere schema mal definido ou coluna legada |
| Risco | Baixo impacto funcional, mas espaco desperdicado e inconsistencia de padrao |
| Recomendacao | Verificar e ajustar para varchar(2) na tabela de destino |

### 🟡 ISSUE-06: ConnectionManager sem Initial Catalog

| Campo | Detalhe |
|---|---|
| Connection | 10.100.86.89 (ADO.NET) |
| Problema | Esta connection manager nao tem Initial Catalog definido. Nao esta em uso aparente no fluxo, mas sua existencia pode causar confusao |
| Recomendacao | Remover se nao estiver em uso ou documentar sua finalidade |

### 🟡 ISSUE-07: Provider legado SQLOLEDB.1

| Campo | Detalhe |
|---|---|
| Conexoes | DBSTAGE e SOFTRANS - TRANSLUTE usam SQLOLEDB.1 |
| Problema | SQLOLEDB.1 e driver legado, descontinuado pela Microsoft. Nao suporta recursos modernos do SQL Server |
| Recomendacao | Migrar para MSOLEDBSQL ou MSOLEDBSQL.1 |

### 🟡 ISSUE-08: Error Output desconectado no destino

| Campo | Detalhe |
|---|---|
| Componente | Destino OLE DB |
| Output | Saída de Erro de Destino OLE DB |
| Problema | Error output definido mas nao conectado a nenhum destino de erros |
| Risco | Linhas rejeitadas sao perdidas sem rastreabilidade |
| Recomendacao | Conectar a tabela de rejeicoes ou arquivo de log |

### 🔵 ISSUE-09: Ausencia de auditoria de execucao

| Campo | Detalhe |
|---|---|
| Problema | Nao ha variaveis ou log de auditoria (data execucao, linhas carregadas, duracao) |
| Recomendacao | Adicionar tabela de controle de carga ou SSIS logging |

## Checklist de Melhorias

- [ ] Substituir DELETE sem WHERE por TRUNCATE ou DELETE com filtro
- [ ] Corrigir typo: "SOFTRANS" → "SOFTRAN" na connection manager
- [ ] Alinhar precisao de GRIS e Pedagio (p14,s4 vs p15,s2)
- [ ] Corrigir tamanho de CTe Substituido (3 vs 5)
- [ ] Verificar e corrigir tamanho de Origem UF e Destino UF (3 vs 2)
- [ ] Migrar providers SQLOLEDB.1 para MSOLEDBSQL
- [ ] Remover ou documentar a connection 10.100.86.89 sem Initial Catalog
- [ ] Conectar error output do destino a tabela de rejeicoes
- [ ] Adicionar auditoria de execucao (data, linhas, duracao)
- [ ] Implementar estrategia de rollback em caso de falha durante a carga
