# ETL_Painel_CTE — Issues e Observações

## Legenda de Severidade

| Ícone | Severidade   | Descrição                                              |
|-------|--------------|--------------------------------------------------------|
| 🔴    | Crítico      | Pode causar falha de carga ou corrupção de dados       |
| 🟠    | Alto         | Impacta qualidade de dados ou manutenibilidade         |
| 🟡    | Médio        | Boa prática não seguida, risco indireto                |
| 🔵    | Informativo  | Observação de design, sem impacto imediato             |

---

## Issues Identificados

### 1. 🟠 Datas armazenadas como VARCHAR (string)

**Colunas afetadas:**
- `DtEmissao` (wstr 30)
- `dtentrega` (wstr 30)
- `DtEmissaoFatura` (wstr 30)
- `dtvencimento` (wstr 30)
- `DtUltimaOcorrencia` (wstr 30)
- `Previsão de Entrega` (wstr 30)

**Motivo:** A query usa `CONVERT(VARCHAR, ..., 103)` que gera strings no formato `DD/MM/YYYY`.

**Impacto:** Impossibilidade de filtros de range de datas eficientes no DW, impossibilidade de comparação direta com `datetime`, agrupamentos mensais/anuais incorretos.

**Recomendação:** Remover os CONVERT para VARCHAR e deixar as colunas como `datetime` / `date` na tabela destino.

---

### 2. 🟡 Coluna `Dt Inclusao Ocorrência` e `Dt Ocorrencia` como string de 61 chars

**Motivo:** A query concatena data+hora: `CONVERT(VARCHAR, ..., 103) + ' ' + CONVERT(VARCHAR, ..., 108)` resultando em "DD/MM/YYYY HH:MM:SS" (19 chars), mas o metadado define comprimento 61 — superestimado.

**Impacto:** Desperdício de espaço; comprimento 19 seria suficiente.

---

### 3. 🟠 Colunas com nomes contendo espaços e caracteres especiais

**Colunas afetadas:**
- `Previsão de Entrega` (tem acento e espaços)
- `Dt Inclusao Ocorrência` (tem acento e espaços)
- `Dt Ocorrencia` (tem espaço)
- `Descrição Ult Ocorrencia` (tem acento e espaço)
- `Usuário Ocorrencia` (tem acento e espaço)

**Impacto:** Requer delimitadores (`[` `]` ou `"` `"`) em qualquer query SQL que use essas colunas. Dificulta integração com ferramentas que não suportam nomes com caracteres especiais.

**Recomendação:** Renomear para nomes sem acentos e sem espaços (ex: `Previsao_Entrega`, `DtInclusaoOcorrencia`).

---

### 4. 🟡 Typo: "Redepacho" (sem s)

Na CASE de `DsTipoEmissao`, o valor 11 é mapeado como `'Redepacho'` mas o correto seria `'Redespacho'` (com s, igual ao valor 9 que é 'Redespacho Prop').

**Linha SQL:**
```sql
WHEN ISNULL(a.intipoemissao, 0) = 11 THEN 'Redepacho'
```
Deve ser:
```sql
WHEN ISNULL(a.intipoemissao, 0) = 11 THEN 'Redespacho'
```

---

### 5. 🟠 validateExternalMetadata = False no Destination

O componente ADO NET Destination tem `validateExternalMetadata="False"`. Isso significa que o SSIS não valida a estrutura da tabela destino em tempo de design/validação.

**Risco:** Alterações na estrutura da tabela `fBaseCTE` passarão despercebidas até o momento da execução, causando falha em produção.

**Recomendação:** Ativar a validação de metadados externos (`validateExternalMetadata="True"`) e realizar testes após qualquer alteração de schema.

---

### 6. 🟡 Error Output no Destination configurado como FailComponent (sem tratamento de erros)

O `ADO NET Destination` tem `errorRowDisposition="FailComponent"`. Qualquer linha com erro de conversão fará o package falhar completamente sem registro do erro.

**Recomendação:** Criar um destino de erro (ex: arquivo de log ou tabela de rejeitos) conectado ao error output.

---

### 7. 🟡 Coluna `NrNotaFiscal` como nText (LOB)

O tipo `nText` é obsoleto desde o SQL Server 2005. No metadado externo, o comprimento é `2147483647` (MAX).

**Recomendação:** Alterar para `NVARCHAR(MAX)` na tabela destino e ajustar o metadado SSIS para `wstr` sem limite fixo.

---

### 8. 🟡 Conversão implícita cy → numeric no Destination

11 colunas do tipo `cy` (currency) na origem chegam ao destino como `numeric p=19 s=255`. A propriedade `AllowImplicitStringConversion=true` está habilitada. Embora funcione, é uma conversão implícita não controlada.

**Recomendação:** Adicionar `Data Conversion` explícito ou ajustar os tipos na query SQL de origem para usar `DECIMAL(19,4)` em vez de `MONEY`.

---

### 9. 🔵 Ausência de variáveis — filtro de data hardcoded

O filtro `AND a.dtemissao >= '20240101'` está embutido diretamente na query SQL. Não há variável SSIS para controlar o período de carga.

**Impacto:** Para alterar o período de carga é necessário editar o package. Dificulta reprocessamento histórico.

**Recomendação:** Criar variável `User::StartDate` (como nos outros packages do mesmo projeto) e parametrizar o filtro.

---

### 10. 🔵 Conexão `sisclifa` (ee) declarada mas não utilizada em SELECT

A tabela `sisclifa` é juntada via `LEFT JOIN sisclifa AS ee WITH (NOLOCK) ON ee.cdinscricao = e.cdinscricao` mas nenhuma coluna de `ee` aparece no SELECT.

**Impacto:** Overhead de JOIN desnecessário na query, especialmente em produção com grandes volumes.

**Recomendação:** Remover o JOIN se não utilizado.

---

### 11. 🔵 ProtectionLevel = 0 (sem proteção de credenciais)

O package não criptografa senhas (`ProtectionLevel="0"` = DontSaveSensitive). As senhas não estão no arquivo, mas isso significa que o package depende de configuração externa (ex: SSIS Catalog ou configuração de ambiente) para funcionar em produção.

**Recomendação:** Documentar o mecanismo de fornecimento de credenciais para este package.

---

## Checklist de Melhorias

- [ ] Converter colunas de data de VARCHAR para DATE/DATETIME no destino
- [ ] Corrigir typo "Redepacho" → "Redespacho" na CASE de DsTipoEmissao
- [ ] Renomear colunas com espaços e acentos
- [ ] Ativar validateExternalMetadata no Destination
- [ ] Implementar tratamento de erros (error output → tabela de rejeitos)
- [ ] Migrar tipo NrNotaFiscal de nText para NVARCHAR(MAX)
- [ ] Remover JOIN desnecessário com sisclifa (alias ee)
- [ ] Criar variável User::StartDate para parametrizar filtro de data
- [ ] Adicionar conversão explícita para colunas cy → numeric
- [ ] Documentar mecanismo de credenciais (ProtectionLevel=0)
