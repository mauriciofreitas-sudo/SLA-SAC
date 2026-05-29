# Issues e Observações — ETL_ControladoriaFrete

## Resumo

| Severidade | Quantidade |
|---|:---:|
| 🔴 Crítico | 2 |
| 🟠 Alto | 2 |
| 🟡 Médio | 3 |
| 🔵 Baixo | 1 |

---

## 🔴 Crítico

### [ISS-01] Dependência de ordem de execução não garantida pelo SSIS

**Contexto:**
`fControladoriaFrete` é gerada a partir da view `VW_CONTROLADORIA_DE_FRETE`, que cruza dados de CT-e, Fichas e CTRBs diretamente no servidor SOFTRAN (169.57.181.231). **O ETL não depende das tabelas do DW** — lê da fonte transacional.

Porém, se a intenção futura for cruzar dados já no DW (usando `fBaseCTE`, `fFichaViagem` e `fCVLD`), a ordem de execução será crítica.

**Impacto:**
- Se os ETLs predecessores (`ETL_BaseCTE`, `ETL_Fichas`, `ETL_CVLD`) ainda estiverem rodando enquanto este inicia, os dados no DW ficam inconsistentes.

**Recomendação:**
Criar um job SQL Server Agent com sequência explícita:
```
1. ETL_Fichas
2. ETL_BaseCTE
3. ETL_CVLD
4. ETL_ControladoriaFrete   ← só após 1, 2 e 3 concluírem com sucesso
```

---

### [ISS-02] CT-es sem CTRB vinculado podem não aparecer

**Contexto:**
A view provavelmente usa `INNER JOIN` entre CT-e e CTRB via ficha de viagem. CTEs emitidos sem ficha de viagem associada (por ex. fretes fracionados diretos, redespacho ou CTEs cancelados) podem ser excluídos da view.

**Impacto:**
- `fControladoriaFrete` pode subreportar o volume total de CTEs.
- Divergência entre totais de `fBaseCTE` (todos os CTEs) e `fControladoriaFrete` (apenas CTEs com CTRB).

**Ação recomendada:**
Verificar se o JOIN da view é INNER ou LEFT. Se INNER, documentar quais CTEs são excluídos e por quê.

---

## 🟠 Alto

### [ISS-03] Full-refresh em tabela com JOINs pesados — risco de timeout

**Contexto:**
A view `VW_CONTROLADORIA_DE_FRETE` une três entidades com histórico de vários anos. O volume de dados pode ser significativamente maior que as outras tabelas fato.

**Impacto:**
O timeout de 600s pode ser insuficiente para volumes históricos grandes.

**Recomendação:**
Monitorar o tempo de execução. Se ultrapassar 400s regularmente, avaliar:
- Carga incremental por `AnoMes`
- Particionamento da view na origem

---

### [ISS-04] Error Outputs desconectados

Tanto Source quanto Destination têm Error Output sem handler conectado. Falhas não são rastreadas.

**Correção:** conectar error outputs a tabela `dbo.fControladoriaFrete_Erros`.

---

## 🟡 Médio

### [ISS-05] Duas conexões redundantes ao destino

Mesmo padrão dos outros ETLs — `sqldba1` (OLE DB) e `datalc` (ADO.NET) apontam para o mesmo servidor/banco. Consolidar em uma conexão ADO.NET.

---

### [ISS-06] Sem log de auditoria de carga

```sql
CREATE TABLE dbo.LogETL_ControladoriaFrete (
    IdLog       int IDENTITY PRIMARY KEY,
    DtInicio    datetime,
    DtFim       datetime,
    NrRegistros int,
    DsStatus    varchar(20),
    DsMensagem  varchar(500)
)
```

---

### [ISS-07] `validateExternalMetadata="False"` no Source

A view é complexa — mudanças de schema (ex: adição de coluna de encargo) passam despercebidas em design-time. Habilitar validação e sincronizar metadados periodicamente.

---

## 🔵 Baixo

### [ISS-08] Task "Truncate" executa DELETE

Mesmo padrão dos demais ETLs. `TRUNCATE TABLE` seria mais eficiente, mas requer verificação de FKs antes de substituir.

---

## Checklist de Melhoria

- [ ] Criar job SQL Agent com sequência de execução explícita (ETL_Fichas → ETL_BaseCTE → ETL_CVLD → ETL_ControladoriaFrete)
- [ ] Verificar se o JOIN da view é INNER ou LEFT e documentar o impacto
- [ ] Monitorar tempo de execução e avaliar carga incremental se timeout for recorrente
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Consolidar conexões redundantes ao destino
- [ ] Implementar tabela de auditoria de carga
- [ ] Habilitar `validateExternalMetadata`
- [ ] Avaliar substituição de `DELETE` por `TRUNCATE`
