# ETL_Performance_V2 (1) — Issues e Observações

## Legenda de Severidade

| Ícone | Severidade   | Descrição                                              |
|-------|--------------|--------------------------------------------------------|
| 🔴    | Crítico      | Pode causar falha de carga ou corrupção de dados       |
| 🟠    | Alto         | Impacta qualidade de dados ou manutenibilidade         |
| 🟡    | Médio        | Boa prática não seguida, risco indireto                |
| 🔵    | Informativo  | Observação de design, sem impacto imediato             |

---

## Melhorias em Relação ao ETL_Performance_V2.dtsx

Esta versão **corrige** os seguintes issues da versão anterior:
- DELETE agora tem filtro de data (parcialmente resolve o issue #1 do V2)
- Drivers OLEDB atualizados de SQLNCLI11.1 para MSOLEDBSQL

---

## Issues Identificados

### 1. 🟠 Inconsistência no nome da coluna filtrada no DELETE

**No mapeamento SSIS:** coluna chama `NFDtEmissao` (camelCase com `t` minúsculo)

**No WHERE do DELETE:**
```sql
WHERE NFDTEmissao >= DATEADD(MONTH, -1, GETDATE())
```
Usa `NFDTEmissao` (com `DT` maiúsculo).

**Impacto:** No SQL Server com collation case-insensitive (padrão), funciona sem erro. Porém, é uma inconsistência de nomenclatura que dificulta manutenção e pode quebrar em collations case-sensitive.

**Recomendação:** Padronizar o nome para `NFDtEmissao` tanto no mapeamento quanto no DELETE.

---

### 2. 🟠 Possível sobreposição de dados entre DELETE e INSERT

O DELETE remove registros do **último mês** (`>= DATEADD(MONTH,-1, GETDATE())`), mas o INSERT carrega **todos** os registros da `vw_fPerformance` sem filtro.

**Cenário problemático:** Se a view retornar dados de mais de 1 mês atrás que já existem na tabela, eles serão inseridos em duplicidade (pois o DELETE não os removeu).

**Recomendação:** Garantir que a view `vw_fPerformance` retorne apenas dados do período equivalente ao DELETE, ou ampliar o DELETE para cobrir o mesmo período da view.

---

### 3. 🟠 Task GetDate 1M continua DESABILITADA

Mesmo nesta versão atualizada, `GetDate 1M` permanece com `DTS:Disabled="True"`.

**Impacto:** O DELETE usa `GETDATE()` diretamente (não usa `User::StartDate`), então a task desabilitada não afeta o DELETE. Mas a variável `User::StartDate` está vinculada ao parâmetro do DELETE sem WHERE correspondente — código morto.

**Recomendação:** Remover o `ParameterBinding` da task DELETE ou documentar por que a variável está vinculada.

---

### 4. 🟠 Conexões de DWGrupolc declaradas mas não utilizadas

Mesmo que os drivers OLEDB tenham sido atualizados, as conexões `10.100.86.89.DWGrupolc.datalc` e `10.100.86.89.DWGrupolc.sqldba1` continuam declaradas sem uso.

---

### 5. 🟠 validateExternalMetadata = False no Destination

Mesmo issue do V2 original — sem validação de metadados externos.

---

### 6. 🟡 Error Output sem tratamento

`errorRowDisposition="FailComponent"` — sem redirecionamento de erros.

---

### 7. 🔵 Dois arquivos para o mesmo package em produção

A coexistência de `ETL_Performance_V2.dtsx` e `ETL_Performance_V2 (1).dtsx` sugere que um é o arquivo de trabalho e o outro é backup ou versão em teste.

**Risco:** Se ambos forem implantados no servidor SSIS sem controle de versão, podem ser executados em paralelo ou em conflito.

**Recomendação:** Definir qual versão é a oficial, remover a outra ou arquivá-la com nomenclatura clara (ex: `ETL_Performance_V2_obsoleto.dtsx`).

---

### 8. 🔵 VersionBuild 106 — confirma que é versão mais nova

`ETL_Performance_V2 (1).dtsx` tem `VersionBuild="106"` vs. `95` do original. Esta versão com o WHILE/DELETE filtrado é a versão mais recente e deve ser a versão de produção.

---

## Checklist de Melhorias

- [ ] Padronizar o nome da coluna: `NFDTEmissao` → `NFDtEmissao` no DELETE
- [ ] Alinhar período do DELETE com o período retornado pela view (evitar duplicatas)
- [ ] Remover ParameterBinding desnecessário na task DELETE
- [ ] Remover conexões não utilizadas (DWGrupolc)
- [ ] Ativar validateExternalMetadata no Destination
- [ ] Implementar tratamento de erros (error output)
- [ ] Definir arquivo oficial vs. backup e remover duplicata
- [ ] Documentar e versionar a view dbo.vw_fPerformance
