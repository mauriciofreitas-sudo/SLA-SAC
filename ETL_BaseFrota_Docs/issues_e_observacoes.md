# ETL_BaseFrota — Issues e Observacoes

## Tabela de Severidades

| Simbolo | Nivel | Descricao |
|---|---|---|
| 🔴 | Critico | Pode causar falha em producao ou perda de dados |
| 🟠 | Alto | Risco operacional significativo ou dado incorreto |
| 🟡 | Medio | Problema de qualidade ou manutencao |
| 🔵 | Baixo | Sugestao de melhoria ou padrao |

## Issues Encontrados

### 🟠 ISSUE-01: Error Output desconectado na Origem

| Campo | Detalhe |
|---|---|
| Componente | ADO NET Source |
| Output | ADO NET Source Error Output |
| Problema | O output de erro da origem esta definido mas nao esta conectado a nenhum destino (ex: arquivo de log ou tabela de erros) |
| Risco | Erros de leitura podem passar despercebidos ou causar falha silenciosa |
| Recomendacao | Conectar o error output a um destino (arquivo CSV ou tabela de erros) e configurar logging |

### 🟠 ISSUE-02: Error Output desconectado no Destino

| Campo | Detalhe |
|---|---|
| Componente | ADO NET Destination |
| Output | ADO NET Destination Error Output |
| Problema | O output de erro do destino nao esta conectado a nenhum tratamento |
| Risco | Linhas rejeitadas na carga sao perdidas sem rastreabilidade |
| Recomendacao | Direcionar linhas de erro para tabela `fBaseFrota_Erros` ou arquivo de log |

### 🟡 ISSUE-03: Duas conexoes redundantes para o mesmo servidor de destino

| Campo | Detalhe |
|---|---|
| Conexoes | `10.100.86.89.DWGrupolc.sqldba` (ADO.NET) e `10.100.86.89.DWGrupolc.sqldba1` (OLEDB) |
| Problema | O mesmo servidor/banco/usuario possui duas connection managers com tipos diferentes. A ADO.NET e usada pelo destino do Data Flow; a OLEDB e usada apenas para o TRUNCATE |
| Risco | Confusao de manutencao; possivel autenticacao dupla |
| Recomendacao | Padronizar ambas as conexoes no mesmo tipo (preferencialmente OLEDB com MSOLEDBSQL) |

### 🟡 ISSUE-04: CommandTimeout assimetrico

| Campo | Detalhe |
|---|---|
| Comparacao | Origem: 600s / Destino: 30s |
| Problema | O timeout da origem e 600 segundos, mas o timeout do destino ADO.NET e apenas 30 segundos. Se a carga demorar mais de 30s, o destino ira expirar |
| Risco | Falha intermitente em execucoes com alto volume de dados |
| Recomendacao | Alinhar ambos os timeouts: definir o destino para 600s ou 0 (infinito) |

### 🟡 ISSUE-05: validateExternalMetadata = False no Destino

| Campo | Detalhe |
|---|---|
| Componente | ADO NET Destination |
| Problema | A validacao de metadados externos esta desativada, o que significa que incompatibilidades de schema entre o SSIS e a tabela de destino nao sao detectadas em tempo de design |
| Risco | Erros de schema so serao descobertos em runtime |
| Recomendacao | Habilitar `validateExternalMetadata = True` apos estabilizar o schema |

### 🔵 ISSUE-06: Ausencia de logging e auditoria de execucao

| Campo | Detalhe |
|---|---|
| Problema | O package nao possui tabelas ou variaveis de auditoria (data de execucao, numero de linhas carregadas, etc.) |
| Risco | Impossivel monitorar historico de cargas ou detectar execucoes incompletas |
| Recomendacao | Adicionar variavel `@[User::RowsLoaded]` e logging via SSIS Logging ou tabela de controle |

### 🔵 ISSUE-07: Ausencia de DelayValidation na task ExecuteSQL

| Campo | Detalhe |
|---|---|
| Problema | A task `Truncate fBaseFrota` nao tem DelayValidation=True, ao contrario do Data Flow que tem. Se a conexao OLEDB estiver indisponivel no momento da validacao, o package falha antes de iniciar |
| Recomendacao | Avaliar se DelayValidation deve ser habilitado de forma consistente em todas as tasks |

## Checklist de Melhorias

- [ ] Conectar error outputs (origem e destino) a um log de erros
- [ ] Alinhar CommandTimeout entre origem (600s) e destino (30s → 600s)
- [ ] Avaliar consolidacao das duas conexoes para DWGrupolc em uma unica (OLEDB)
- [ ] Habilitar validateExternalMetadata no destino ADO.NET
- [ ] Adicionar variaveis de auditoria (data execucao, linhas carregadas)
- [ ] Implementar logging SSIS para rastreabilidade
- [ ] Documentar schedule/agendamento deste package no SQL Server Agent
- [ ] Verificar se a view `vw_ConsultaVeiculos` possui indice adequado para suportar carga completa
