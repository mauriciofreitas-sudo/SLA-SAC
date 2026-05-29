# ETL_Romaneios1 — Issues e Observações

## Tabela Resumo de Severidades

| Severidade | Quantidade | Descrição Geral                             |
|------------|------------|---------------------------------------------|
| ALTA       | 2          | Problemas que podem causar falha ou perda de dados |
| MEDIA      | 3          | Problemas de design que afetam manutenibilidade |
| BAIXA      | 3          | Melhorias de boas práticas                   |

---

## Issues Encontrados

### [ALTA-01] DELETE sem WHERE — apaga toda a tabela

**Localização:** Task `Truncate fFicha`
**SQL:** `DELETE FROM dbo.fFichaViagem`

O package possui a variável `User::StartDate = 01/01/2025` e um `ParameterBinding` vinculando-a ao parâmetro `0` da task SQL. Porém o SQL não possui nenhum `?` — o parâmetro nunca é usado. O DELETE apaga **todos** os registros da tabela, independentemente da data. Isso torna a variável `StartDate` completamente sem efeito e o processo opera como full reload incondicionalmente.

**Risco:** Se a view de origem retornar dados parciais por qualquer falha, a tabela destino ficará vazia após o DELETE.

**Sugestão:** Usar `TRUNCATE TABLE dbo.fFichaViagem` (mais rápido) se a intenção é full reload, OU adicionar `WHERE DtEmissao >= ?` no DELETE se quiser carga incremental.

---

### [ALTA-02] Error Output do Source desconectado

**Localização:** `ADO NET Source` e `ADO NET Destination` no `Load fFicha`

Ambos possuem Error Outputs (`ADO NET Source Error Output` e `ADO NET Destination Error Output`) mas não estão conectados a nenhum destino (flat file, tabela de log, etc.). Erros de linha serão propagados como falha do componente e o package falhará sem registrar quais linhas causaram o problema.

**Sugestão:** Conectar o Error Output a uma tabela de log ou arquivo de erro para rastreabilidade.

---

### [MEDIA-01] Variável `StartDate` não utilizada

**Localização:** `DTS:Variables` — `User::StartDate`

A variável existe, tem valor padrão `01/01/2025` e está vinculada via ParameterBinding ao DELETE, mas o SQL não a utiliza. É um artefato morto que pode confundir mantenedores sobre qual é o escopo de dados carregados.

**Sugestão:** Remover a variável e o ParameterBinding, ou implementar o filtro no SQL.

---

### [MEDIA-02] 40 colunas da tabela destino sem mapeamento

**Localização:** Schema externo de `ADO NET Destination`

A tabela `fFichaViagem` possui pelo menos 91 colunas no total (51 mapeadas + 40 não mapeadas como NrDiasAtraso, Status, DtPrevisaoEntregaCTeNova, FichaViagem, Hora, IDCTEPERF, CNPJRemetente, NomeRemetente, NFSerie, NFNr, NFVlr, NFDtEmissao, CTeEmpresa, CTeNr, etc.). Essas colunas ficam com NULL após o reload.

**Sugestão:** Verificar se outras tasks/packages populam essas colunas via UPDATE pós-carga, ou se são colunas obsoletas que deveriam ser removidas.

---

### [MEDIA-03] Conexões duplicadas para o mesmo servidor/banco

**Localização:** `ConnectionManagers`

Existem duas conexões para `10.100.86.89 / DWGrupolc`:
- `10.100.86.89.DWGrupolc.datalc` (ADO.NET — usada pelo Destination)
- `10.100.86.89.DWGrupolc.sqldba1` (OLEDB — usada pelo ExecuteSQL)

Ambas apontam para o mesmo servidor e banco, mas usam providers diferentes (ADO.NET vs OLEDB). Isso duplica a gestão de credenciais.

**Sugestão:** Unificar em uma única connection manager ou pelo menos documentar o motivo de usar providers diferentes.

---

### [BAIXA-01] Timeout assimétrico entre Source e Destination

| Componente          | Timeout    |
|---------------------|------------|
| ADO NET Source      | 600 segundos |
| ADO NET Destination | 30 segundos |

O destino tem timeout 20x menor que a origem. Para grandes volumes de dados, a inserção pode expirar antes de completar.

**Sugestão:** Alinhar os timeouts ou definir o destino como 0 (infinito) para bulk loads.

---

### [BAIXA-02] Sem auditoria de carga

Não há registro de: data/hora de execução, quantidade de linhas inseridas, ou resultado da operação em nenhuma tabela de controle.

**Sugestão:** Adicionar uma task de auditoria ao final do fluxo registrando `@@ROWCOUNT` e timestamp.

---

### [BAIXA-03] Nome do package diverge do arquivo

O `DTS:ObjectName` do package é `ETL_Romaneios` (sem o `1`), mas o arquivo chama-se `ETL_Romaneios1.dtsx`.

**Sugestão:** Alinhar o nome interno com o nome do arquivo para evitar confusão na documentação e nos logs de execução.

---

## Checklist de Melhoria

- [ ] Corrigir ou documentar o comportamento do DELETE sem WHERE
- [ ] Remover ou implementar a variável `StartDate`
- [ ] Conectar Error Outputs a destinos de log
- [ ] Alinhar timeouts entre Source (600s) e Destination (30s)
- [ ] Investigar as 40 colunas não mapeadas na tabela destino
- [ ] Consolidar as duas conexões para DWGrupolc
- [ ] Adicionar tabela/task de auditoria de carga
- [ ] Alinhar nome interno do package (`ETL_Romaneios`) com nome do arquivo (`ETL_Romaneios1.dtsx`)
