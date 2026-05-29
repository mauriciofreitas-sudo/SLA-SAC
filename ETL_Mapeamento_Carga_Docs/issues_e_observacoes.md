# ETL_Mapeamento_Carga — Issues e Observacoes

## Resumo de Severidades

| Severidade | Quantidade |
|---|---|
| CRITICO | 1 |
| ALTO | 3 |
| MEDIO | 3 |
| BAIXO | 2 |
| **Total** | **9** |

## Issues Encontrados

### CRITICO-01 — Typo no ObjectName do package: "MepeamentoCarga"
**Descricao:** O ObjectName do package e `SSIS_stg_MepeamentoCarga` (com "Mepeamento" em vez de "Mapeamento").
**Impacto:** Nome errado persiste em logs do SQL Server Agent, catalogos SSISDB e relatorios de monitoramento. Busca por "Mapeamento" nao encontra o package.
**Recomendacao:** Corrigir o ObjectName para `SSIS_stg_MapeamentoCarga` e atualizar todos os jobs que fazem referencia ao package.

### ALTO-01 — 6 campos de data armazenados como DT_STR(30)
**Descricao:** As colunas `Previsao de Entrega`, `Data de Emissao CTE`, `DtSaidaParceiro`, `DtSaidachegadadestino`, `DataRomaneio` e `DtUltimaOcorrencia` chegam como varchar(30) e sao inseridas como string no destino.
**Impacto:** Impossibilita calculos de SLA, ordenacao correta, filtros por periodo e joins com dimensao de data. Valores como "01/05/2024 00:00:00" nao sao comparaveis diretamente.
**Recomendacao:** Adicionar um Derived Column ou Data Conversion para converter para DT_DBTIMESTAMP antes do destino. Alternativa: converter na view de origem.

### ALTO-02 — Saidas de erro desconectadas
**Descricao:** Ambos os componentes (Origem e Destino) possuem saidas de erro nao conectadas.
**Impacto:** Erros silenciosos — linhas rejeitadas nao sao rastreadas.
**Recomendacao:** Conectar a tabela de log de erros.

### ALTO-03 — DELETE sem WHERE na tblBasePrincipal
**Descricao:** `DELETE FROM [dbo].[tblBasePrincipal]` sem filtro — apaga tudo.
**Impacto:** Janela de downtime entre DELETE e fim do INSERT. Tabela possivelmente usada por outros processos.
**Recomendacao:** Investigar se a tabela tem dependencias. Avaliar TRUNCATE (mais rapido) ou carga incremental.

### MEDIO-01 — Flags de motorista com DT_STR(1) de semantica obscura
**Descricao:** `Motorista Manifesto` e `Motorista romaneio` sao campos varchar(1) — provavelmente flags "S/N" ou "0/1".
**Impacto:** Sem documentacao de dominio, o significado e ambiguo para consumidores downstream.
**Recomendacao:** Documentar os valores possiveis e considerar adicionar Derived Column com label descritivo.

### MEDIO-02 — Volume mapeado como DT_CY (currency)
**Descricao:** O campo `Volume` usa DT_CY (currency/money) para armazenar volume fisico de carga.
**Impacto:** Tipo semanticamente incorreto — volume deveria ser DT_R8 (float) ou DT_NUMERIC.
**Recomendacao:** Verificar o tipo na origem e corrigir se necessario.

### MEDIO-03 — Sem log de auditoria
**Descricao:** Nenhum log de contagem de linhas ou status de execucao.
**Recomendacao:** Implementar variavel `@RowCount` capturada apos o Data Flow.

### BAIXO-01 — TABLOCK com possivel concorrencia
**Descricao:** FastLoadOptions inclui TABLOCK na `tblBasePrincipal`. Se a tabela for consultada por relatorios durante a carga, havera bloqueio.
**Recomendacao:** Verificar horario de execucao e uso concorrente da tabela.

### BAIXO-02 — CommandTimeout = 0
**Descricao:** Sem timeout definido no source e no destino.
**Recomendacao:** Definir 600s para source (query complexa), 300s para destino.

## Checklist de Melhoria

- [ ] Corrigir typo ObjectName: "MepeamentoCarga" -> "MapeamentoCarga"
- [ ] Converter 6 campos de data de DT_STR para DT_DBTIMESTAMP
- [ ] Conectar saidas de erro a tabela de log
- [ ] Avaliar TRUNCATE em vez de DELETE para a tblBasePrincipal
- [ ] Documentar semantica dos campos flag (Motorista Manifesto, Motorista romaneio)
- [ ] Corrigir tipo do campo Volume (DT_CY -> DT_R8 ou DT_NUMERIC)
- [ ] Implementar log de auditoria de execucao
- [ ] Avaliar TABLOCK versus uso concorrente da tabela
- [ ] Definir CommandTimeout nos componentes
