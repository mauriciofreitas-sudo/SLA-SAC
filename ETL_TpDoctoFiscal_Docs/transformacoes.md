# ETL_TpDoctoFiscal — Transformações

## Resumo

Este package é **passthrough puro**. Não há nenhum componente de transformação no pipeline:
- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Conditional Split
- Sem Aggregate

O dado flui diretamente da tabela `sistdf` (origem SOFTRAN_TRANSLUTE) para a tabela `[dbo].[tb_SERIE]` (DBStage) sem modificação.

---

## Diagrama ASCII do Data Flow

```
[Origem OLE DB 1]              [Destino OLE DB 1]
sistdf                  -->    [dbo].[tb_SERIE]
169.57.181.231                 10.100.86.89
softran_translute              DBStage
SELECT * WITH(NOLOCK)          FastLoad (TABLOCK)
(8 colunas)                    (7 colunas mapeadas)
                               (1 coluna NrSerieERPSen: não mapeada)
```

---

## Pré-processamento SQL

**Task: Tarefa Executar SQL**
```sql
DELETE FROM [dbo].[tb_SERIE]
```

Executado via OLEDB (DBStage), ThreadHint=0, dentro do Sequence Container.

---

## Controle de Fluxo — Sequence Container

O uso do `Contêiner da Sequência` (Sequence Container) encapsula as duas tasks:
1. `Tarefa Executar SQL` — apaga a tabela
2. `Tarefa Fluxo de Dados` — recarrega

A `PrecedenceConstraint` garante que o Data Flow só inicia após o DELETE concluir com sucesso.

---

## Mapeamento de Tipos

| Tipo na Origem (SOFTRAN) | Tipo SSIS (str/i4) | Tipo no Destino (DBStage) | Observação |
|--------------------------|--------------------|---------------------------|------------|
| VARCHAR(40) — DsTpDoctoFiscal | str, 40, cp1252 | VARCHAR(40), cp1252 | Direto |
| VARCHAR(10) — DsApelido | str, 10, cp1252 | VARCHAR(10), cp1252 | Direto |
| VARCHAR(3)  — NrSerie   | str, 3, cp1252  | VARCHAR(3), cp1252  | Direto |
| INT — CdTpDoctoFiscal   | i4              | INT                 | Direto |
| INT — InTipoDocumento   | i4              | INT                 | Direto |
| INT — NrModelo          | i4              | INT                 | Direto |
| INT — InFormaImpressao  | i4              | INT                 | Direto |
| VARCHAR(5) — NrSerieERPSen | str, 5, cp1252 | VARCHAR(5) — NAO MAPEADO | Perdido |

---

## Observação sobre FastLoad vs DELETE

O destino usa `AccessMode=3` (FastLoad) com `TABLOCK`, o que é adequado para operações de bulk load. No entanto, o DELETE anterior é feito na mesma conexão OLEDB que o insert — `TABLOCK` no FastLoad não garante atomicidade com o DELETE anterior. Se o SSIS falhar entre o DELETE e o INSERT, a tabela ficará vazia.

**Comparação de abordagens:**
| Abordagem | Atomicidade | Performance |
|-----------|-------------|-------------|
| DELETE + INSERT (atual) | Não atômica | Adequada |
| TRUNCATE + INSERT | Não atômica (mais rápida) | Melhor |
| BEGIN TRAN + DELETE + INSERT + COMMIT | Atômica | Mais lenta |
