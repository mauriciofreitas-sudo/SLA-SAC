# Issues e Observações — ETL_Antecipa

## Resumo

| Severidade | Quantidade |
|------------|------------|
| 🔴 Critico | 0 |
| 🟠 Alto | 2 |
| 🟡 Medio | 3 |
| 🔵 Baixo | 2 |
| **Total** | **7** |

---

## Issues Identificados

### 🟠 ISSUE-001 — Colunas de log na tabela destino sem mapeamento

**Contexto:** Ambas as tabelas de destino (`fFornecedoresSenior` e `fNfSenior`) possuem colunas de log nos metadados externos:
- `fFornecedoresSenior`: `logs_dt_envio` (dbTimeStamp) e `logs_sts_envio` (wstr 30)
- `fNfSenior`: `log_dt_envio` (dbTimeStamp) e `log_sts_envio` (wstr 30)

Nenhuma dessas colunas é mapeada ou recebe valor do pipeline.

**Impacto:** As colunas ficarão com NULL ou valor default em todas as linhas inseridas. Se houver lógica de controle que dependa dessas colunas, ela não funcionará.

**Correcao sugerida:** Mapear as colunas de log (ex.: `logs_dt_envio = GETDATE()`, `logs_sts_envio = 'OK'`) usando Derived Columns, ou remover as colunas se forem legado.

---

### 🟠 ISSUE-002 — Inconsistência de nomenclatura nas colunas de log

**Contexto:** A tabela `fFornecedoresSenior` usa prefixo `logs_` (plural: `logs_dt_envio`, `logs_sts_envio`) enquanto `fNfSenior` usa `log_` (singular: `log_dt_envio`, `log_sts_envio`).

**Impacto:** Inconsistência de padrão de nomenclatura dificulta manutenção e queries padronizadas entre tabelas do mesmo domínio.

**Correcao sugerida:** Padronizar o prefixo das colunas de log em todas as tabelas do Stage.

---

### 🟡 ISSUE-003 — Uso de DELETE em vez de TRUNCATE para limpeza

**Contexto:** As tasks "Truncate Fornecedores" e "Truncate Notas" usam `DELETE FROM` em vez de `TRUNCATE TABLE`.

**Impacto:** `DELETE` gera log de transação completo e é significativamente mais lento para tabelas grandes. Também não reseta o IDENTITY counter.

**Correcao sugerida:** Usar `TRUNCATE TABLE fFornecedoresSenior` e `TRUNCATE TABLE dbo.fNfSenior` (verificar se há FKs que impeçam). Ou manter DELETE mas garantir que seja a opção intencional.

---

### 🟡 ISSUE-004 — Tipos nText para colunas NF e SerieNotaFiscal

**Contexto:** As colunas `NF` e `SerieNotaFiscal` chegam da source como `nText` (tipo legado, depreciado no SQL Server). No destino são registradas como `wstr(MAX)`.

**Impacto:** `nText` é obsoleto desde SQL Server 2005. Pode causar problemas de performance e incompatibilidades. O SSIS precisa de conversão implícita.

**Correcao sugerida:** Verificar se a view `VW_int_s_LuCash` usa colunas TEXT/NTEXT na origem e converter para VARCHAR(MAX)/NVARCHAR(MAX). Atualizar o tipo na source do SSIS.

---

### 🟡 ISSUE-005 — Error Outputs desconectados em todos os componentes

**Contexto:** Nenhum dos error outputs (Source ou Destination) está conectado a um destino de log.

**Impacto:** Erros de linha individual não são registrados; apenas o package falha sem indicar quais registros causaram o problema.

**Correcao sugerida:** Conectar os error outputs a uma tabela de erros ou arquivo de log.

---

### 🔵 ISSUE-006 — Tamanho inconsistente de CNPJ: CNPJFornecedor(14) vs CNPJComprador(18)

**Contexto:** `CNPJFornecedor` tem tamanho 14 (formato sem máscara: 14 dígitos) e `CNPJComprador` tem tamanho 18 (formato com máscara: XX.XXX.XXX/XXXX-XX).

**Impacto:** Os dois campos representam CNPJs mas em formatos diferentes. Isso impede comparações diretas entre eles e pode indicar inconsistência na view de origem.

**Correcao sugerida:** Padronizar o formato de CNPJ para a mesma representação em ambas as colunas.

---

### 🔵 ISSUE-007 — Nome do Sequence Container genérico ("Sequence Container 1")

**Contexto:** O container é chamado de "Sequence Container 1", nome padrão sem significado de negócio.

**Impacto:** Dificulta a leitura dos logs de execução e manutenção do package.

**Correcao sugerida:** Renomear para algo descritivo como "Carga Antecipacao Recebiveis".

---

## Checklist de Melhoria

- [ ] Mapear ou remover colunas de log (`logs_dt_envio`, `logs_sts_envio`, `log_dt_envio`, `log_sts_envio`)
- [ ] Padronizar nomenclatura de colunas de log (logs_ vs log_)
- [ ] Substituir DELETE por TRUNCATE para melhor performance
- [ ] Verificar e corrigir tipos nText → NVARCHAR(MAX) na view de origem
- [ ] Conectar error outputs a destino de log de erros
- [ ] Padronizar formato de CNPJ nas duas colunas
- [ ] Renomear Sequence Container para nome descritivo
- [ ] Avaliar se as views VW_Int_Pessoa e VW_int_s_LuCash estão atualizadas
