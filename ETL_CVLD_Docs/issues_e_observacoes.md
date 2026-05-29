# Issues e Observações — ETL_CVLD

## Resumo

| Severidade | Quantidade |
|---|:---:|
| 🔴 Crítico | 0 |
| 🟠 Alto | 2 |
| 🟡 Médio | 3 |
| 🔵 Baixo | 2 |

---

## 🟠 Alto

### [ISS-01] Full-refresh apaga dados durante a carga

**Contexto:**
O ETL executa `DELETE FROM dbo.fCVLD` antes de recarregar. Consultas ao dashboard durante esse intervalo retornam valores zerados na seção de Análise de Terceiros.

**Impacto:**
- KPI "Custo Total CTRB" e gráfico "Top 10 Transportadoras" ficam sem dados durante a carga.
- Se o volume for grande, a janela pode ser perceptível.

**Correção sugerida:**
```sql
-- Carga em tabela stage e swap atômico:
-- 1. Carregar em dbo.fCVLD_Stage
-- 2. Renomear: fCVLD → fCVLD_Old, fCVLD_Stage → fCVLD
-- 3. DROP fCVLD_Old
```

---

### [ISS-02] Error Outputs desconectados

**Contexto:**
Nem o Source nem o Destination têm Error Output conectado a um handler.

**Impacto:**
Falhas de inserção (overflow de tipo, violação de constraint) encerram o pipeline sem identificar o registro problemático.

**Correção sugerida:**
Criar tabela `dbo.fCVLD_Erros` e conectar os error outputs a ela.

---

## 🟡 Médio

### [ISS-03] CTRBs com status `Pendente` incluídos no full-refresh

**Contexto:**
A view `VW_CVLD` provavelmente inclui CTRBs em todos os status (Pago, Pendente, Cancelado). O dashboard agrega `VlSaldoCTRB` para mostrar o total a pagar.

**Impacto:**
Se CTRBs são cancelados retroativamente na fonte, o full-refresh corrige o DW automaticamente — este comportamento é correto para o caso de uso de análise financeira.

**Observação:**
Documentar explicitamente quais status a view expõe para evitar dupla contagem entre `VlFreteCTRB` (valor contratado) e `VlSaldoCTRB` (valor ainda a pagar).

---

### [ISS-04] Duas conexões redundantes ao destino

| ID | Tipo | Servidor | Banco |
|---|---|---|---|
| `sqldba1` | OLE DB | 10.100.86.89 | DWGrupolc |
| `datalc` (DW) | ADO.NET | 10.100.86.89 | DWGrupolc |

Manutenção duplicada de credenciais — consolidar em uma conexão ADO.NET.

---

### [ISS-05] Sem log de auditoria de carga

Não há registro de data/hora de execução, quantidade de CTRBs carregados ou status de sucesso/falha.

```sql
CREATE TABLE dbo.LogETL_CVLD (
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

Mesmo padrão dos outros ETLs. `DELETE` é mais lento que `TRUNCATE TABLE` para volumes grandes. Verificar FKs antes de substituir.

---

### [ISS-07] `validateExternalMetadata="False"` no Source

Mudanças de schema em `VW_CVLD` não são detectadas em design-time. Habilitar a validação e sincronizar metadados periodicamente.

---

## Checklist de Melhoria

- [ ] Implementar carga em stage + swap para eliminar janela de indisponibilidade
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Documentar quais status de CTRB são incluídos na view
- [ ] Consolidar conexões redundantes ao destino
- [ ] Implementar tabela de auditoria de carga
- [ ] Avaliar substituição de `DELETE` por `TRUNCATE`
- [ ] Habilitar `validateExternalMetadata`
