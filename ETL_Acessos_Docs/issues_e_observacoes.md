# Issues e Observações — ETL_Acessos

## Resumo

| Severidade | Quantidade |
|------------|------------|
| 🔴 Crítico | 0 |
| 🟠 Alto | 1 |
| 🟡 Médio | 2 |
| 🔵 Baixo | 2 |
| **Total** | **5** |

---

## Issues Identificados

### 🟠 ISSUE-001 — Variável `StartDate` declarada e não utilizada

**Contexto:** A variável `User::StartDate` está declarada com valor `01/01/2025`, mas não é referenciada em nenhum componente, expressão ou constraint do package.

**Impacto:** Código morto aumenta confusão na manutenção. Pode indicar que a extração deveria ser incremental (filtrada por data) mas está carregando tudo.

**Correção sugerida:** Remover a variável se não houver uso planejado, ou implementar filtro incremental na view `vw_acesso_softran` usando esta variável como parâmetro (via SqlCommand com expressão parametrizada).

---

### 🟡 ISSUE-002 — Mismatch de tipo: `ExecutionDt` gerado como `date`, registrado como `dbTimeStamp`

**Contexto:** A Derived Column produz `ExecutionDt` com tipo `date` (apenas data, sem hora). O metadado externo da tabela `fAcessos_Softran` registra esta coluna como `dbTimeStamp` (datetime).

**Impacto:** O SSIS faz conversão implícita, mas a parte de hora sempre será `00:00:00`. Se a intenção for registrar o horário exato de execução, o tipo da Derived Column deve ser corrigido.

**Correção sugerida:** Alterar o tipo de saída da Derived Column de `date` para `dbTimeStamp`, ou confirmar que apenas a data é necessária e ajustar o metadado externo.

---

### 🟡 ISSUE-003 — Error Outputs desconectados em todos os componentes

**Contexto:** Os três componentes (ADO NET Source, Derived Column, ADO NET Destination) possuem saídas de erro configuradas como `FailComponent`, e nenhum error output está conectado a um destino de log/tratamento.

**Impacto:** Qualquer erro em tempo de execução vai falhar o package inteiro, sem registro de quais linhas causaram o problema. Dificulta depuração em produção.

**Correção sugerida:** Conectar os error outputs a um destino de log (ex.: tabela de erros ou arquivo flat), especialmente no ADO NET Destination onde erros de constraint são mais comuns.

---

### 🔵 ISSUE-004 — Conexão OLEDB `sqldba1` declarada e não utilizada

**Contexto:** O Connection Manager `10.100.86.89.DWGrupolc.sqldba1` (OLEDB/SQLNCLI11.1) está definido no package mas não é referenciado por nenhum componente.

**Impacto:** Conexão órfã aumenta o tamanho do arquivo e pode tentar conectar desnecessariamente ao inicializar o package, gerando delay.

**Correção sugerida:** Remover o Connection Manager `10.100.86.89.DWGrupolc.sqldba1` do package.

---

### 🔵 ISSUE-005 — Nome do Data Flow Task é genérico ("Load fPerf")

**Contexto:** O único Data Flow Task é chamado `Load fPerf`, que é aparentemente um nome residual de outro package (possivelmente copiado de um template de performance).

**Impacto:** O nome não descreve o que o task faz (carregar acessos Softran). Dificulta leitura do package no SSIS Designer e em logs de execução.

**Correção sugerida:** Renomear para `Load fAcessos_Softran` ou `Load Acessos`.

---

## Checklist de Melhoria

- [ ] Remover variável `StartDate` ou implementar carga incremental
- [ ] Corrigir tipo de saída da Derived Column `ExecutionDt` (date → dbTimeStamp)
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Remover Connection Manager OLEDB `sqldba1` não utilizado
- [ ] Renomear Data Flow Task de "Load fPerf" para nome descritivo
- [ ] Avaliar se a carga deve ser incremental (por data) ou full replace (com TRUNCATE antes)
- [ ] Documentar a view `vw_acesso_softran` no servidor de origem
