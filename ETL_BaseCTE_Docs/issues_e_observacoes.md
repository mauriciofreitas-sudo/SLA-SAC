# Issues e Observações — ETL_BaseCTE

## Resumo

| Severidade | Quantidade |
|---|:---:|
| 🔴 Crítico | 1 |
| 🟠 Alto | 2 |
| 🟡 Médio | 2 |
| 🔵 Baixo | 2 |

---

## 🔴 Crítico

### [ISS-01] Full-refresh sem janela incremental — risco de indisponibilidade

**Contexto:**
O ETL apaga toda a tabela `dbo.fBaseCTE` antes de recarregar. Como `fBaseCTE` é a principal fonte do Dashboard Executivo, durante a janela entre o DELETE e o fim do INSERT a tabela fica vazia.

**Impacto:**
- O dashboard exibe zerado ou sem dados durante a carga
- Se o volume for grande (3 anos de histórico), a janela pode durar minutos

**Correção sugerida:**
```sql
-- Opção 1: carga em tabela stage + swap
-- 1. Carregar em dbo.fBaseCTE_Stage
-- 2. RENAME: fBaseCTE → fBaseCTE_Old, fBaseCTE_Stage → fBaseCTE
-- 3. DROP fBaseCTE_Old

-- Opção 2: carga incremental por AnoMes
DELETE FROM dbo.fBaseCTE WHERE AnoMes >= ?
-- e filtrar a view de origem pelo mesmo período
```

---

## 🟠 Alto

### [ISS-02] Error Outputs desconectados

**Contexto:**
ADO NET Source e ADO NET Destination possuem Error Output configurado mas não conectado a nenhum handler.

**Impacto:**
Qualquer erro de conversão ou inserção encerra o pipeline sem registro do registro problemático.

**Correção sugerida:**
Conectar ambos os error outputs a uma tabela `dbo.fBaseCTE_Erros` ou flat file de log.

---

### [ISS-03] Duas conexões redundantes ao destino

**Contexto:**

| ID | Tipo | Servidor | Banco |
|---|---|---|---|
| `sqldba1` | OLE DB | 10.100.86.89 | DWGrupolc |
| `datalc` (DW) | ADO.NET | 10.100.86.89 | DWGrupolc |

**Impacto:**
Manutenção duplicada de credenciais. Senha alterada em um lugar pode quebrar o outro.

---

## 🟡 Médio

### [ISS-04] `validateExternalMetadata="False"` no Source

**Contexto:**
Validação de schema da view desabilitada no componente de origem.

**Impacto:**
Alterações em `vwCTE_Base` (renomear coluna, mudar tipo) só são detectadas na execução, nunca em design-time.

---

### [ISS-05] Sem log de auditoria de carga

**Contexto:**
Não há registro de data/hora de execução, quantidade de registros processados ou status.

**Sugestão:**
```sql
CREATE TABLE dbo.LogETL_BaseCTE (
    IdLog       int IDENTITY PRIMARY KEY,
    DtInicio    datetime,
    DtFim       datetime,
    NrRegistros int,
    DsStatus    varchar(20),
    DsMensagem  varchar(500)
)
```

---

## 🔵 Baixo

### [ISS-06] Task nomeada "Truncate" executa DELETE

Mesmo padrão do ETL_Fichas: a task chama-se "Truncate" mas usa `DELETE FROM`, que é mais lento e gera mais log de transação que `TRUNCATE TABLE`. Verifique FKs antes de substituir.

---

### [ISS-07] Timeout Source/Destination deve ser simétrico

Confirmar que ambos os componentes têm timeout 600s. Timeout assimétrico (Source alto / Destination baixo) pode causar falha no INSERT mesmo com leitura bem-sucedida.

---

## Checklist de Melhoria

- [ ] Avaliar carga incremental por `AnoMes` para eliminar janela de indisponibilidade
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Consolidar as duas conexões ao destino em uma só
- [ ] Habilitar `validateExternalMetadata`
- [ ] Implementar tabela de auditoria de carga
- [ ] Avaliar substituição de `DELETE` por `TRUNCATE` (verificar FKs antes)
- [ ] Confirmar simetria de timeouts entre Source e Destination
