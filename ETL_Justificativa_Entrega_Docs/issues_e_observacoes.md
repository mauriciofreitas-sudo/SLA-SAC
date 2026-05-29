# ETL_Justificativa_Entrega — Issues e Observacoes

## Resumo de Severidades

| Severidade | Quantidade |
|---|---|
| CRITICO | 0 |
| ALTO | 2 |
| MEDIO | 2 |
| BAIXO | 2 |
| **Total** | **6** |

## Issues Encontrados

### ALTO-01 — Saida de Erro desconectada
**Componente:** Origem Just_Entrega e Justificativa_Entrega (destino)
**Descricao:** Ambos os componentes possuem saida de erro (`Saida de Erro de Origem OLE DB` e `Saida de Erro de Destino OLE DB`) que nao estao conectadas a nenhum componente de destino (flat file de log, tabela de erros, etc.).
**Impacto:** Erros de conversao ou insercao sao silenciados ou causam falha total do package, sem rastreabilidade.
**Recomendacao:** Conectar as saidas de erro a uma tabela `stg_Erro_JustificativaEntrega` ou arquivo de log com timestamp, ErrorCode e ErrorColumn.

### ALTO-02 — Nome da conexao de origem com typo: "SOFTRA - TRANSLUTE"
**Descricao:** O ConnectionManager de origem se chama "SOFTRA - TRANSLUTE" em vez de "SOFTRAN - TRANSLUTE". O typo esta no ObjectName, nao na connection string (que aponta corretamente para softran_translute).
**Impacto:** Confusao de manutencao, diferente dos demais packages do mesmo projeto que usam "SOFTRAN".
**Recomendacao:** Renomear para "SOFTRAN - TRANSLUTE" para padronizacao.

### MEDIO-01 — DELETE sem WHERE (carga full sem janela temporal)
**Descricao:** `delete from [dbo].[stg_Justificativa_Entrega]` sem clausula WHERE apaga todos os registros independente de data.
**Impacto:** Se a carga falhar apos o DELETE e antes do INSERT completo, a tabela fica vazia. Risco de downtime no painel.
**Recomendacao:** Considerar carga incremental por data ou envolver DELETE+INSERT em transacao explicita.

### MEDIO-02 — Sem log de auditoria
**Descricao:** Nao ha registro de data/hora de inicio/fim da carga, numero de linhas processadas ou status de execucao.
**Impacto:** Dificuldade de monitoramento e troubleshooting.
**Recomendacao:** Adicionar variaveis de contagem de linhas e gravar em tabela de log de ETL.

### BAIXO-01 — CommandTimeout = 0 em ambos os componentes
**Descricao:** Tanto o Source quanto o Destination tem CommandTimeout=0 (infinito).
**Impacto:** Uma query travada nunca expira, podendo bloquear o servidor indefinidamente.
**Recomendacao:** Definir timeout razoavel (ex: 300s para source, 120s para destino).

### BAIXO-02 — TABLOCK no destino pode causar bloqueio
**Descricao:** FastLoadOptions inclui TABLOCK, que adquire lock exclusivo na tabela `stg_Justificativa_Entrega` durante toda a carga.
**Impacto:** Se algum processo (relatorio, query) tentar ler a tabela durante a carga, sera bloqueado.
**Recomendacao:** Avaliar se TABLOCK e necessario. Remover se a tabela for consultada concorrentemente.

## Checklist de Melhoria

- [ ] Conectar saidas de erro a destino de log
- [ ] Corrigir typo no nome da conexao ("SOFTRA" -> "SOFTRAN")
- [ ] Adicionar clausula WHERE no DELETE (ou usar transacao)
- [ ] Implementar log de auditoria (linhas processadas, duracao)
- [ ] Definir CommandTimeout adequado nos componentes
- [ ] Avaliar remocao de TABLOCK se tabela e consultada durante ETL
- [ ] Adicionar tratamento de falha com notificacao (Send Mail Task)
- [ ] Documentar janela de execucao agendada
