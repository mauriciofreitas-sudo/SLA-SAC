# ETL_Painel_CRD — Issues e Observacoes

## Resumo de Severidades

| Severidade | Quantidade |
|---|---|
| CRITICO | 1 |
| ALTO | 4 |
| MEDIO | 3 |
| BAIXO | 3 |
| **Total** | **11** |

## Issues Encontrados

### CRITICO-01 — DELETE sem WHERE na maior tabela de staging (stg_CRDBase)
**Descricao:** `DELETE FROM [dbo].[stg_CRDBase]` sem clausula WHERE apaga todos os registros. Esta tabela recebe 41 colunas com janela de 350 dias de dados — potencialmente milhoes de linhas.
**Impacto:** Janela de downtime significativa entre o DELETE e o fim do INSERT (query com timeout=300s). Se o package falhar apos o DELETE, o painel CRD fica sem dados.
**Recomendacao:** Substituir por TRUNCATE TABLE (mais rapido, menos log) ou implementar carga incremental com DELETE por janela de data.

### ALTO-01 — Typo na query do Fluxo 2: espaco ausente antes de "with"
**Descricao:** A query do "Origem CRD" e `select * from [dbo].[vw_FichaViagens]with (nolock)` — falta espaco entre o nome da view e o hint WITH (NOLOCK).
**Impacto:** Em algumas versoes do SQL Server pode causar erro de sintaxe. Mesmo funcionando, e um erro de codigo que pode criar falsos-positivos em ferramentas de analise.
**Recomendacao:** Corrigir para `select * from [dbo].[vw_FichaViagens] with (nolock)`.

### ALTO-02 — Saidas de erro desconectadas em ambos os fluxos
**Descricao:** Os componentes `Destino OLE DB` (stg_CRDBase) e `DEST CRD` (stg_Base_Ficha_Viagens), assim como as origens, possuem saidas de erro nao conectadas.
**Impacto:** Erros de insercao ou conversao sao silenciados.
**Recomendacao:** Redirecionar erros para tabela de log.

### ALTO-03 — Fluxo 2 (Fichas) sem task de DELETE/TRUNCATE previa
**Descricao:** O "Sequence Container" que carrega `stg_Base_Ficha_Viagens` nao possui task de limpeza antes do Data Flow.
**Impacto:** A tabela pode acumular duplicatas a cada execucao (comportamento de APPEND). Se o package for executado diariamente, haverá dados duplicados.
**Recomendacao:** Adicionar uma `Execute SQL Task` com `TRUNCATE TABLE stg_Base_Ficha_Viagens` antes do Data Flow de fichas.

### ALTO-04 — CommandTimeout=300s no Fluxo 1, mas 0 no Fluxo 2
**Descricao:** O Origem do Fluxo 1 tem timeout=300s; o Origem CRD do Fluxo 2 tem timeout=0 (infinito). Os destinos de ambos tem timeout=0.
**Impacto:** Assimetria de configuracao. Uma query travada no Fluxo 2 nunca expira.
**Recomendacao:** Padronizar timeouts em todos os componentes.

### MEDIO-01 — Coluna "Empresa Ficha" com espaco no nome
**Descricao:** A coluna 40 do stg_CRDBase se chama `Empresa Ficha` (com espaco).
**Impacto:** Exige aspas ao referenciar em SQL (`[Empresa Ficha]`), aumenta risco de erro em queries manuais.
**Recomendacao:** Renomear para `EmpresaFicha` ou `CdEmpresaFicha`.

### MEDIO-02 — DW_GRUPOLC declarado mas aparentemente nao usado nos fluxos ativos
**Descricao:** O ConnectionManager `DW_GRUPOLC` esta declarado mas nao aparece nas conexoes dos componentes dos dois Data Flows analisados.
**Impacto:** Conexao ociosa, possivel vestigio de refatoracao antiga.
**Recomendacao:** Verificar se ha tasks nao documentadas que usam esta conexao; remover se inutilizada.

### MEDIO-03 — Sem log de auditoria nos dois fluxos paralelos
**Descricao:** Nenhum registro de linhas processadas, duracao ou status em nenhum dos dois containers.
**Recomendacao:** Adicionar log de execucao apos cada Data Flow.

### BAIXO-01 — TABLOCK em ambos os destinos
**Descricao:** `stg_CRDBase` e `stg_Base_Ficha_Viagens` usam TABLOCK durante a carga.
**Impacto:** Bloqueia leituras concorrentes nas tabelas de staging durante ETL.
**Recomendacao:** Avaliar se ha consumidores concorrentes. Considerar remover TABLOCK se a janela de ETL for bem definida.

### BAIXO-02 — DelayValidation=True no Data Flow de Fichas
**Descricao:** O Data Flow "DATA LAKE - CRD - Fichas viagens" tem `DelayValidation="True"`.
**Impacto:** Erros de schema/conexao so sao detectados em tempo de execucao, nao em tempo de validacao. Dificulta deteccao precoce de problemas.
**Recomendacao:** Remover DelayValidation apos confirmar que os metadados estao corretos e atualizados.

### BAIXO-03 — Application Name desatualizado nas connection strings
**Descricao:** A connection string do DBSTAGE referencia `SSIS-SSIS_stg_Custo_Folha-...` (nome de outro package antigo).
**Impacto:** No SQL Server Profiler/Activity Monitor, as queries aparecem com nome de package incorreto, dificultando troubleshooting.
**Recomendacao:** Atualizar Application Name para `SSIS-SSIS_stg_Painel_CRD-...`.

## Checklist de Melhoria

- [ ] Substituir DELETE por TRUNCATE no Fluxo 1 (stg_CRDBase)
- [ ] Corrigir typo na query: `[vw_FichaViagens]with` -> `[vw_FichaViagens] with`
- [ ] Conectar saidas de erro a tabela de log em ambos os fluxos
- [ ] Adicionar TRUNCATE/DELETE antes do Fluxo 2 (stg_Base_Ficha_Viagens)
- [ ] Padronizar CommandTimeout nos 4 componentes de origem/destino
- [ ] Renomear coluna "Empresa Ficha" para remover espaco
- [ ] Verificar e remover ConnectionManager DW_GRUPOLC se inutilizado
- [ ] Implementar log de auditoria de execucao
- [ ] Avaliar TABLOCK nos destinos
- [ ] Remover DelayValidation do Fluxo 2 apos estabilizacao
- [ ] Corrigir Application Name na connection string do DBSTAGE
