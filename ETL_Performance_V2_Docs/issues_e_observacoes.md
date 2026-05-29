# ETL_Performance_V2 — Issues e Observações

## Legenda de Severidade

| Ícone | Severidade   | Descrição                                              |
|-------|--------------|--------------------------------------------------------|
| 🔴    | Crítico      | Pode causar falha de carga ou corrupção de dados       |
| 🟠    | Alto         | Impacta qualidade de dados ou manutenibilidade         |
| 🟡    | Médio        | Boa prática não seguida, risco indireto                |
| 🔵    | Informativo  | Observação de design, sem impacto imediato             |

---

## Issues Identificados

### 1. 🔴 DELETE sem WHERE — apaga toda a tabela a cada execução

A task "Truncate fPerf" executa:
```sql
DELETE FROM dbo.fPerformance
```

Apesar de ter `User::StartDate` vinculado como parâmetro, **não há cláusula WHERE** que use esse parâmetro. O DELETE apaga **todos** os registros da tabela.

**Impacto:** Cada execução do package descarta todo o histórico existente, tornando a tabela um snapshot instantâneo sem histórico.

**Intenção aparente:** Provavelmente deveria ser:
```sql
DELETE FROM dbo.fPerformance WHERE NFDtEmissao >= ?
```
(usando `User::StartDate` como parâmetro de corte)

**Recomendação:** Revisar a lógica — ou usar TRUNCATE TABLE explicitamente (mais eficiente), ou implementar o DELETE com filtro correto se a intenção for carga incremental.

---

### 2. 🟠 Task GetDate 1M está DESABILITADA

A task que calcula a data de início do mês anterior (`GetDate 1M`) está com `DTS:Disabled="True"`.

**Efeito:** A variável `User::StartDate` mantém o valor padrão `1/1/2025` permanentemente.

**Impacto:** O parâmetro de data nunca é atualizado dinamicamente. Mesmo que o DELETE tivesse um WHERE, usaria sempre `1/1/2025`.

**Recomendação:** Habilitar a task ou remover a dependência se não for necessária.

---

### 3. 🟠 Conexões de DWGrupolc declaradas mas não utilizadas

Duas conexões com o banco `DWGrupolc` estão declaradas no package mas não são utilizadas por nenhuma task ativa:
- `10.100.86.89.DWGrupolc.datalc` (ADO.NET)
- `10.100.86.89.DWGrupolc.sqldba1` (OLEDB)

**Impacto:** Conexões desnecessárias aumentam o tempo de inicialização do package e podem causar falha se o banco estiver indisponível no momento da execução.

**Recomendação:** Remover as conexões não utilizadas.

---

### 4. 🟠 Conexões OLEDB com driver legado SQLNCLI11.1

Duas conexões OLEDB usam `Provider=SQLNCLI11.1` (SQL Server Native Client 11, descontinuado):
- `10.100.86.89.DBStage.sqldba1`
- `169.57.181.231.SOFTRAN_TRANSLUTE.softran`

**Impacto:** O SQLNCLI11.1 não é suportado oficialmente em versões modernas. Pode causar incompatibilidade em atualizações de infraestrutura.

**Recomendação:** Migrar para `MSOLEDBSQL` (Microsoft OLE DB Driver for SQL Server).

---

### 5. 🟠 validateExternalMetadata = False no Destination

O componente ADO NET Destination tem `validateExternalMetadata="False"`.

**Risco:** Alterações no schema de `fPerformance` passarão despercebidas até a execução.

---

### 6. 🟡 Error Output no Destination sem tratamento

`errorRowDisposition="FailComponent"` — qualquer linha com erro de inserção faz o package falhar sem log da linha problemática.

**Recomendação:** Implementar redirecionamento de erros para tabela de rejeitos ou arquivo de log.

---

### 7. 🔵 Fonte de dados é uma view (caixa-preta)

A origem é `dbo.vw_fPerformance` e não há SQL inline visível no package. A lógica de negócio está encapsulada na view no servidor de origem.

**Impacto:** Qualquer alteração na view impacta diretamente o package sem alertas.

**Recomendação:** Documentar a view `dbo.vw_fPerformance` separadamente e versionar seu DDL.

---

### 8. 🔵 VersionBuild = 95 (v2) vs. 106 (v2 cópia)

O arquivo `ETL_Performance_V2.dtsx` tem `VersionBuild="95"` enquanto `ETL_Performance_V2 (1).dtsx` tem `VersionBuild="106"`. Ver `ETL_Performance_V2_1_Docs` para detalhes das diferenças.

---

## Checklist de Melhorias

- [ ] Corrigir o DELETE para incluir WHERE com filtro de data OU usar TRUNCATE TABLE
- [ ] Habilitar/revisar a task GetDate 1M (atualmente desabilitada)
- [ ] Remover conexões não utilizadas (DWGrupolc ADO.NET e OLEDB)
- [ ] Atualizar driver OLEDB de SQLNCLI11.1 para MSOLEDBSQL
- [ ] Ativar validateExternalMetadata no Destination
- [ ] Implementar tratamento de erros (error output)
- [ ] Documentar e versionar a view dbo.vw_fPerformance
