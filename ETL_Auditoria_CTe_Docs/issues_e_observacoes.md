# Issues e Observacoes — ETL_Auditoria_CTe

## Resumo

| Severidade | Quantidade |
|------------|------------|
| 🔴 Critico | 1 |
| 🟠 Alto | 3 |
| 🟡 Medio | 4 |
| 🔵 Baixo | 3 |
| **Total** | **11** |

---

## Issues Identificados

### 🔴 ISSUE-001 — Uso de SQLOLEDB.1 (driver legado/depreciado)

**Contexto:** Ambas as conexoes usam `Provider=SQLOLEDB.1`, o driver OLE DB original da Microsoft, depreciado desde 2011.

**Impacto:** O driver SQLOLEDB.1 nao suporta criptografia TLS 1.2+, nao recebe atualizacoes de seguranca, e tem comportamentos inconsistentes com SQL Server 2019+. Pode causar falhas de conexao apos atualizacoes do servidor.

**Correcao sugerida:** Migrar para `MSOLEDBSQL` (Microsoft OLE DB Driver for SQL Server) ou usar ADO.NET (SqlClient), que e o padrao usado nos outros packages (ETL_Acessos, ETL_Antecipa).

---

### 🟠 ISSUE-002 — Nome do package com inconsistencia de case (CTE vs CTe)

**Contexto:** O arquivo se chama `ETL_Auditoria_CTe.dtsx` mas o ObjectName interno e `ETL_Auditoria_CTE` (tudo maiusculo no final). O Sequence Container se chama "Contêiner da Sequência" (portugues, diferente dos outros packages que usam ingles).

**Impacto:** Inconsistencia pode causar confusao em logs, catalogo do SSIS, e referencias em jobs SQL Agent. O container em portugues indica que este package foi criado em uma maquina com SSDT/SSIS em portugues, diferente dos demais.

**Correcao sugerida:** Padronizar o ObjectName e nomes dos componentes para ingles, alinhado com os demais packages.

---

### 🟠 ISSUE-003 — Coluna `CdProdCliente` na source mas nao mapeada no destino

**Contexto:** A `Origem Cte Produto` (vw_AuditoriaCTe_Prod) retorna 5 colunas, incluindo `CdProdCliente`, mas o destino `Auditoria_CTe_Cod_Prod` so mapeia 4 colunas (sem `CdProdCliente`).

**Impacto:** O campo `CdProdCliente` e trafegado pelo pipeline mas descartado antes de chegar ao destino. Se essa coluna for necessaria para analise, os dados estao sendo perdidos.

**Correcao sugerida:** Verificar se `CdProdCliente` deveria ser incluido na tabela `Auditoria_CTe_Cod_Prod`, ou se a view esta retornando uma coluna desnecessaria.

---

### 🟠 ISSUE-004 — Uso de DELETE em vez de TRUNCATE (3 tasks)

**Contexto:** As 3 tasks de limpeza usam `DELETE FROM` ao inves de `TRUNCATE TABLE`:
- `Delete from [dbo].[Auditoria_CTe_Cod_Prod]`
- `Delete from [Auditoria_CTe_Cod_Prod_v2]`
- `Delete from [dbo].[stg_Auditoria_CTe]`

**Impacto:** Para a tabela `stg_Auditoria_CTe` com 79 colunas e potencialmente muitos registros, `DELETE` gera log de transacao completo e e muito mais lento que `TRUNCATE`. Aumenta o risco de timeout e crescimento excessivo de log.

**Correcao sugerida:** Usar `TRUNCATE TABLE` nas 3 tasks (verificar ausencia de FKs e triggers).

---

### 🟡 ISSUE-005 — FastLoadMaxInsertCommitSize = 2147483647 (commit unico)

**Contexto:** Todos os 3 destinos OLE DB tem `FastLoadMaxInsertCommitSize` configurado como `2147483647` (valor maximo, equivalente a commit apenas ao final).

**Impacto:** Em caso de erro durante o carregamento de um volume grande, todo o trabalho e perdido e o rollback pode ser extremamente lento. Aumenta o risco de crescimento excessivo de log de transacao.

**Correcao sugerida:** Configurar um tamanho de batch razoavel (ex.: 10000 ou 50000 linhas) para permitir commits intermediarios.

---

### 🟡 ISSUE-006 — Strings em ANSI (str/cp1252) — risco de encoding

**Contexto:** O package usa tipo `str` (ANSI, codepage 1252) ao inves de `wstr` (Unicode). Conexoes OLEDB tipicamente operam em ANSI.

**Impacto:** Nomes de destinatarios, remetentes, cidades e outras strings com caracteres especiais (acentos, cedilha) estao sujeitos a corrupção se houver qualquer ponto do pipeline que nao respeite o codepage 1252.

**Correcao sugerida:** Considerar migrar para ADO.NET (que usa Unicode nativamente) como nos outros packages, ou garantir que as collations das tabelas de destino sejam compativeis com cp1252.

---

### 🟡 ISSUE-007 — Error Outputs desconectados em todos os componentes (6 outputs)

**Contexto:** Os 6 componentes (3 sources + 3 destinations) possuem saidas de erro nao conectadas.

**Impacto:** Qualquer erro de linha individual falha o package inteiro sem registro de quais linhas causaram o problema.

**Correcao sugerida:** Conectar os error outputs a uma tabela de log de erros com contexto (data, package, componente, linha).

---

### 🟡 ISSUE-008 — Tabela `stg_Auditoria_CTe` filtrada por cliente Michelin

**Contexto:** A SQL da source principal e `select * from [dbo].[vw_Auditoria_CTe_Michelin] with (nolock)`. O nome da view sugere que esta filtrada para o cliente Michelin.

**Impacto:** O package se apresenta como auditoria geral de CT-e mas na pratica carrega dados de apenas um cliente. Isso pode ser intencional, mas deve ser documentado explicitamente. Se o package for reutilizado para outros clientes, a view precisara ser alterada ou parametrizada.

**Correcao sugerida:** Documentar explicitamente que o escopo e o cliente Michelin. Avaliar se ha necessidade de generalizacao com parametro de cliente.

---

### 🔵 ISSUE-009 — Nomes de colunas inconsistentes: mistura de camelCase, PascalCase e lowercase

**Contexto:** Na tabela `stg_Auditoria_CTe` existem colunas com diferentes convencoes de nome:
- PascalCase: `DsApelidoEmitente`, `VlFretePeso`, `DtEmissao`
- camelCase: `cdempresa`, `dsusuario`, `dsapelido`, `dsnatureza`, `dtentrega`, `dtvencimento`

**Impacto:** Inconsistencia dificulta escrita de queries e aumenta chances de erro de digitacao.

**Correcao sugerida:** Padronizar para PascalCase (parao mais comum no dataset: cdEmpresa → CdEmpresa).

---

### 🔵 ISSUE-010 — `with (nolock)` em todas as queries da source

**Contexto:** Todas as 3 queries usam `with (nolock)`.

**Impacto:** Embora acelere a leitura, `NOLOCK` pode retornar dados sujos (linhas nao commitadas ou deletadas) e causar resultados incorretos em cenarios de alta concorrencia.

**Correcao sugerida:** Avaliar se o risco de leitura suja e aceitavel para dados de auditoria. Se precisar de consistencia, usar snapshot isolation no banco de origem.

---

### 🔵 ISSUE-011 — Ausencia de parametrizacao de data (carga sempre completa)

**Contexto:** O package apaga e recarrega 100% dos dados em cada execucao.

**Impacto:** Para volumes grandes de CT-e, a carga completa pode ser inviavel em producao. Nao ha variavel de data de corte ou mecanismo incremental.

**Correcao sugerida:** Avaliar implementacao de carga incremental por data de emissao ou data de modificacao.

---

## Checklist de Melhoria

- [ ] Migrar conexoes de SQLOLEDB.1 para MSOLEDBSQL ou ADO.NET
- [ ] Padronizar nomes (ObjectName CTE → CTe, containers em ingles)
- [ ] Investigar e tratar coluna `CdProdCliente` nao mapeada
- [ ] Substituir DELETE por TRUNCATE nas 3 tasks de limpeza
- [ ] Configurar FastLoadMaxInsertCommitSize com valor razoavel (ex.: 50000)
- [ ] Avaliar migracao de str (ANSI) para wstr (Unicode)
- [ ] Conectar error outputs a tabela de log de erros
- [ ] Documentar escopo limitado ao cliente Michelin
- [ ] Padronizar nomenclatura de colunas (camelCase vs PascalCase)
- [ ] Avaliar risco de NOLOCK nos dados de auditoria
- [ ] Implementar carga incremental por data
