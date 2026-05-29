# ETL_Sequencias — Issues e Observações

## Tabela Resumo de Severidades

| Severidade | Quantidade | Descrição Geral                                  |
|------------|------------|--------------------------------------------------|
| ALTA       | 3          | Problemas que podem causar perda de dados ou falha |
| MEDIA      | 4          | Problemas de design e performance                |
| BAIXA      | 3          | Melhorias de boas práticas                       |

---

## Issues Encontrados

### [ALTA-01] Datas convertidas para VARCHAR na query — perda de tipo

**Localização:** Query SQL da origem

7 campos de data são convertidos para VARCHAR usando `CONVERT(VARCHAR, ..., 103)`:
- `DtEmissao`, `dtentrega`, `DtEmissaoFatura`, `dtvencimento`, `Previsão de Entrega`, `Dt Inclusao Ocorrência`, `Dt Ocorrencia`

Esses campos chegam ao pipeline como `DT_STR` (texto) em vez de `DT_DBTIMESTAMP`. A tabela destino `fFichasAbertas` também os armazena como texto (formatos "dd/mm/yyyy" ou "dd/mm/yyyy hh:mm:ss").

**Impacto:** Impossibilidade de filtros de data, ordenações cronológicas e cálculos de diferença de datas diretamente na tabela DW.

**Sugestão:** Remover os `CONVERT(VARCHAR, ..., 103)` e entregar as datas com tipo nativo. Formatar apenas na camada de apresentação (Power BI, etc.).

---

### [ALTA-02] Instrução `GO` no final da query SQL

**Localização:** Campo `SqlCommand` do ADO NET Source — última linha: `GO`

A palavra-chave `GO` é um separador de lotes do SQL Server Management Studio, não é SQL padrão. Dependendo do driver ADO.NET, pode causar erro de execução.

**Sugestão:** Remover o `GO` do final da query.

---

### [ALTA-03] Error Output do Source e Destination desconectados

**Localização:** `ADO NET Source` e `ADO NET Destination`

Ambos possuem Error Outputs não conectados. Erros de linha silenciosos ou falha total sem rastreabilidade.

**Sugestão:** Conectar Error Outputs a tabelas de log.

---

### [MEDIA-01] Query extremamente complexa — risco de timeout

**Localização:** SqlCommand do ADO NET Source

A query possui:
- 15+ LEFT JOIN com `WITH(NOLOCK)`
- 12 OUTER APPLY aninhados
- Subconsultas correlacionadas em APPLY
- Filtra dados desde `2024-01-01` sem NOLOCK em alguns joins (gtcfatit, gtcfat, gfatitu, gtcconhe n)

Com grandes volumes de dados históricos, esse query pode ultrapassar o timeout de 600s.

**Sugestão:** Criar uma view otimizada no lado SOFTRAN ou indexar as colunas de join mais frequentes.

---

### [MEDIA-02] ~60% das colunas da query não são carregadas

**Localização:** Query vs. Mapeamento do pipeline

A query calcula/retorna aproximadamente 60+ expressões, mas apenas 37 são mapeadas. Colunas como `DsMotorista`, `NomeMotorista`, `NrRomaneio`, `NrPlacaRomaneio`, `DsSituacao`, `DsTpFrete`, `ArquivoConemb`, `ArquivoOcoren`, etc., são computadas mas descartadas.

**Impacto:** Processamento desnecessário de dados no servidor SOFTRAN.

**Sugestão:** Simplificar a query para retornar apenas as 37 colunas necessárias, ou mover as colunas descartadas para a tabela destino.

---

### [MEDIA-03] Typo em `intipoemissao = 11`: "Redepacho" (sem 's')

**Localização:** CASE WHEN da query de origem

```sql
WHEN ISNULL(a.intipoemissao, 0) = 11 THEN 'Redepacho'
```

Deveria ser `'Redespacho'`. Esse valor fica inconsistente com `intipoemissao = 9` que corretamente retorna `'Redespacho Prop'`.

**Sugestão:** Corrigir para `'Redespacho'`.

---

### [MEDIA-04] Conexões duplicadas para DWGrupolc

| Conexão                         | Provider  | Uso                |
|---------------------------------|-----------|--------------------|
| 10.100.86.89.DWGrupolc.sqldba   | ADO.NET   | Destination INSERT |
| 10.100.86.89.DWGrupolc.sqldba1  | OLEDB     | TRUNCATE           |

Dois providers diferentes para o mesmo banco. Duplicidade de gestão de credenciais.

---

### [BAIXA-01] DelayValidation=True na task Load fBaseSequencias

**Localização:** Atributo `DTS:DelayValidation="True"` no task Load fBaseSequencias

Indica que a validação dos metadados externos é adiada para runtime. Isso pode mascarar incompatibilidades de schema entre a origem e o destino que só serão detectadas durante a execução.

**Sugestão:** Desativar o DelayValidation após estabilizar o schema e realizar uma validação completa.

---

### [BAIXA-02] Filtro de data hardcoded no SQL

```sql
AND a.dtemissao >= '20240101'
```

A data de corte está hardcoded na query. Para atualizar o período de dados, é necessário editar o package.

**Sugestão:** Parametrizar via variável SSIS (similar ao `User::StartDate` do ETL_Romaneios).

---

### [BAIXA-03] Sem auditoria de carga

Não há registro de timestamp, total de linhas ou status em tabela de controle.

---

## Checklist de Melhoria

- [ ] Remover `CONVERT(VARCHAR, ...)` das datas — manter tipos nativos
- [ ] Remover `GO` no final da query SQL
- [ ] Conectar Error Outputs a destinos de log
- [ ] Otimizar query (remover colunas não utilizadas, avaliar índices)
- [ ] Corrigir typo "Redepacho" para "Redespacho" no CASE WHEN
- [ ] Parametrizar data de corte `20240101` via variável SSIS
- [ ] Consolidar conexões ADO.NET e OLEDB para DWGrupolc
- [ ] Desativar DelayValidation após validar schema
- [ ] Adicionar auditoria de carga
