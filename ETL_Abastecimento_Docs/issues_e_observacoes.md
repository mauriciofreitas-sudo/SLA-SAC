# Issues e Observações — ETL_Abastecimento

## Resumo

| Severidade | Quantidade |
|---|:---:|
| 🔴 Crítico | 2 |
| 🟠 Alto | 3 |
| 🟡 Médio | 3 |
| 🔵 Baixo | 2 |

---

## 🔴 Crítico

### [ISS-01] Typo no nome da tabela de origem — `Abasteciemtos`

**Contexto:**
O Fluxo 3 usa a query:
```sql
select* from [dbo].[Abasteciemtos]
```
O nome correto seria `Abastecimentos` (letras 'e' e 'i' invertidas). Além disso, falta espaço após `select`.

**Impacto:**
- O package só funciona se existir uma tabela chamada exatamente `Abasteciemtos` no banco `softran_translute`.
- Se alguém corrigir o nome da tabela na fonte sem atualizar o package, o ETL falha silenciosamente.
- Altamente provável que o nome real seja `Abastecimentos` — confirmar no SOFTRAN.

**Correção:**
```sql
-- Substituir por:
SELECT * FROM [dbo].[Abastecimentos]
-- (verificar nome exato antes de alterar)
```

---

### [ISS-02] Datas armazenadas como texto (`DT_STR`) em dois fluxos

**Contexto:**

| Fluxo | Coluna | Tipo SSIS | Tamanho |
|---|---|---|---|
| Fluxo 3 (DescontoAbastecimento) | `Dtemissao` | `DT_STR` | 30 |
| Fluxo 5 (stg_RazaoVeiculo) | `DtLancamento` | `DT_STR` | 30 |

**Impacto:**
- Qualquer query que filtre por data nessas colunas (`WHERE DtLancamento >= '2026-01-01'`) pode retornar resultados incorretos se o formato for ambíguo (ex: `DD/MM/AAAA` vs `AAAA-MM-DD`).
- Impede uso de funções de data (DATEDIFF, DATEPART) diretamente.
- Ordenação por data será ordenação léxica, não cronológica.

**Ação recomendada:**
1. Verificar o formato real dos dados no SOFTRAN.
2. Adicionar um `Derived Column` ou `Data Conversion` no SSIS para converter para `DT_DBTIMESTAMP`.
3. Ou garantir que a segunda etapa (DW) faça o `CAST/CONVERT` adequado.

---

## 🟠 Alto

### [ISS-03] Destino é DBStage, não DWGrupolc — etapa de promoção não documentada

**Contexto:**
Este package carrega para `DBStage`, uma camada intermediária. Não há documentação sobre:
- Qual package ou procedure transforma os dados do DBStage para o DWGrupolc.
- Quando essa segunda etapa é executada.
- Quais regras de negócio são aplicadas na promoção.

**Impacto:**
Sem a etapa de promoção, os dados nunca chegam ao DW final para análise. Se essa etapa falhar silenciosamente, o DW fica desatualizado sem alertas.

**Ação recomendada:**
Documentar o package/procedure de promoção `DBStage → DWGrupolc` para completar a pipeline.

---

### [ISS-04] Tipo `DT_CY` (money) para Quantidade e Média de Consumo

**Contexto:**
As colunas `Quantidade` (litros), `QtMediaApurada` (km/L) e `ValorTotal` usam tipo `DT_CY` (Currency), que mapeia para `money` no SQL Server.

**Impacto:**
- `money` tem precisão de 4 casas decimais — adequado para valores monetários, mas pode perder precisão para médias de consumo (ex: 8.7543 km/L pode ser arredondado).
- `DT_CY` internamente usa int64 × 10^-4 — operações matemáticas podem ter comportamento inesperado.

**Recomendação:**
Verificar se `decimal(14,4)` ou `float` seriam mais adequados para `Quantidade` e `QtMediaApurada`.

---

### [ISS-05] Error Outputs desconectados em todos os 5 fluxos

**Contexto:**
Nenhum dos 5 pares Source/Destination tem Error Output conectado a um handler de erro.

**Impacto:**
Qualquer erro de conversão ou violação de constraint encerra o fluxo inteiro sem identificar qual registro falhou. Sem possibilidade de reprocessamento seletivo.

**Correção sugerida:**
Criar tabela `dbo.stg_Abastecimento_Erros` e conectar os error outputs — pelo menos nos fluxos 1 e 2 (maior volume).

---

## 🟡 Médio

### [ISS-06] DELETEs usam `delete` minúsculo e formatação inconsistente

**Contexto:**
Os 5 DELETEs têm formatação inconsistente:
- `delete from [dbo].[stg_Abastecimento]`
- `delete From [dbo].[stg_Abastecimento2]` (`From` com F maiúsculo)
- `delete from [dbo].[Stg_PlacaAg_Combustivel]` (tabela com maiúscula mista)

Funciona, mas dificulta leitura e manutenção.

---

### [ISS-07] Sem log de auditoria de carga

O package não registra data/hora de início/fim, quantidade de registros carregados por tabela ou status de sucesso/falha.

```sql
CREATE TABLE dbo.LogETL_Abastecimento (
    IdLog         int IDENTITY PRIMARY KEY,
    NmTabela      varchar(100),
    DtInicio      datetime,
    DtFim         datetime,
    NrRegistros   int,
    DsStatus      varchar(20),
    DsMensagem    varchar(500)
)
```

---

### [ISS-08] `TABLOCK` bloqueia tabelas durante a carga

**Contexto:**
`FastLoadOptions = TABLOCK, CHECK_CONSTRAINTS` adquire um lock de tabela durante o INSERT.

**Impacto:**
Queries concorrentes contra as tabelas staging ficam bloqueadas enquanto o ETL insere. Para staging (sem consultas simultâneas), é aceitável. Se algum relatório ou procedure consultar o staging durante a carga, haverá bloqueio.

---

## 🔵 Baixo

### [ISS-09] DELETEs ao invés de TRUNCATE

Os 5 Execute SQL Tasks usam `DELETE FROM` (sem WHERE). Funcionalmente equivalente ao TRUNCATE para o resultado final, mas mais lento e gera mais log de transação. Verificar FKs antes de substituir por `TRUNCATE TABLE`.

---

### [ISS-10] Codepage 1252 — risco em ambientes com collation diferente

Todos os campos de texto usam `DT_STR` com `codePage=1252` (Latin-1). Se o servidor DBStage usar collation diferente (ex: `Latin1_General_CI_AI` vs `SQL_Latin1_General_CP1_CI_AS`), caracteres especiais (acentos, cedilha) podem ser corrompidos.

---

## Checklist de Melhoria

- [ ] Confirmar nome real da tabela `Abasteciemtos` no SOFTRAN e corrigir no package
- [ ] Adicionar conversão de data para `Dtemissao` e `DtLancamento` (de string para datetime)
- [ ] Documentar e validar o processo de promoção `DBStage → DWGrupolc`
- [ ] Avaliar uso de `decimal` ao invés de `money` para `Quantidade` e `QtMediaApurada`
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Padronizar formatação SQL nos DELETEs
- [ ] Implementar tabela de auditoria de carga
- [ ] Avaliar impacto do `TABLOCK` em consultas concorrentes
- [ ] Avaliar substituição de `DELETE` por `TRUNCATE`
- [ ] Verificar compatibilidade de codepage/collation entre SOFTRAN e DBStage
