# ETL_Romaneios — Issues e Observações

## Legenda de Severidade

| Ícone | Severidade   | Descrição                                              |
|-------|--------------|--------------------------------------------------------|
| 🔴    | Crítico      | Pode causar falha de carga ou corrupção de dados       |
| 🟠    | Alto         | Impacta qualidade de dados ou manutenibilidade         |
| 🟡    | Médio        | Boa prática não seguida, risco indireto                |
| 🔵    | Informativo  | Observação de design, sem impacto imediato             |

---

## Issues Identificados

### 1. 🔴 DELETE sem WHERE — apaga toda a tabela a cada execução

A task "Truncate fFicha" executa:
```sql
DELETE FROM dbo.fFichaViagem
```

Apesar de ter `User::StartDate` vinculado como parâmetro, **não há cláusula WHERE** na query. O DELETE apaga **todos** os registros da tabela `fFichaViagem` a cada execução.

**Impacto:** Perda de qualquer histórico entre execuções. A tabela funciona como snapshot instantâneo.

**Comparação:** O `ETL_Performance_V2 (1).dtsx` já corrigiu este padrão com WHILE+DELETE filtrado. Este package deveria seguir o mesmo caminho.

**Recomendação:** Implementar DELETE com filtro ou usar TRUNCATE TABLE explícito (mais eficiente) se a intenção é realmente limpar tudo.

---

### 2. 🟠 Driver OLEDB legado SQLNCLI11.1

A conexão `10.100.86.89.DWGrupolc.sqldba1` usa `Provider=SQLNCLI11.1` (SQL Server Native Client 11 — descontinuado).

**Impacto:** Pode causar incompatibilidade em atualizações de infraestrutura.

**Recomendação:** Migrar para `MSOLEDBSQL` (como feito no `ETL_Performance_V2 (1).dtsx`).

---

### 3. 🟠 Variável StartDate declarada mas sem uso efetivo

`User::StartDate` (valor padrão `1/1/2025`) está vinculada ao parâmetro do DELETE (`ParameterName="0"`), mas como o DELETE não tem WHERE, o valor nunca é usado.

**Impacto:** Código morto — cria confusão sobre a intenção do package.

**Recomendação:** Remover o `ParameterBinding` ou implementar o filtro correspondente.

---

### 4. 🟠 Fonte de dados é uma view (caixa-preta)

A origem é `dbo.Ficha_de_Viagem_Geral` e não há SQL inline no package. A lógica de negócio está encapsulada na view no servidor de origem.

**Impacto:** Alterações na view impactam diretamente o package sem alertas. O package carrega todos os registros retornados pela view sem filtro.

**Recomendação:** Documentar a view `dbo.Ficha_de_Viagem_Geral` separadamente e versionar seu DDL.

---

### 5. 🟡 Sem tratamento de erros no Destination

`errorRowDisposition="FailComponent"` — qualquer linha com erro de inserção fará o package falhar completamente sem registro da linha problemática.

**Recomendação:** Implementar redirecionamento de erros para tabela de rejeitos.

---

### 6. 🔵 BatchSize = 0 (auto) vs. BatchSize = 50.000 no Performance

O `ETL_Performance_V2.dtsx` usa `BatchSize=50000` explicitamente para melhor controle de memória e performance. Este package usa `BatchSize=0` (SSIS controla automaticamente).

**Impacto:** Para tabelas grandes, o auto pode ser menos eficiente.

**Recomendação:** Definir explicitamente um tamanho de lote (ex: 50.000) para previsibilidade.

---

### 7. 🔵 Nome do arquivo inconsistente com DTS:ObjectName

- **Nome do arquivo:** `ETL_Romaneios.dtsx`
- **DTS:ObjectName:** `ETL_Romaneios`

Os nomes são consistentes. Porém, a tabela de destino é `fFichaViagem` (Ficha de Viagem), e a view de origem é `Ficha_de_Viagem_Geral`. O nome "Romaneios" pode ser confuso — o package carrega dados de Fichas de Viagem, que incluem romaneios, mas o nome pode não refletir o conteúdo completo.

---

### 8. 🔵 ProtectionLevel = 0 com senhas criptografadas

Senhas estão criptografadas individualmente nas connections (DTS:Password com Encrypted="1"), mas o ProtectionLevel geral é 0. Isso é consistente com o padrão dos outros packages do projeto.

---

## Checklist de Melhorias

- [ ] Corrigir o DELETE para incluir WHERE com filtro de data OU usar TRUNCATE TABLE
- [ ] Atualizar driver OLEDB de SQLNCLI11.1 para MSOLEDBSQL
- [ ] Remover ou corrigir o ParameterBinding desnecessário da task DELETE
- [ ] Documentar e versionar a view dbo.Ficha_de_Viagem_Geral
- [ ] Implementar tratamento de erros (error output → tabela de rejeitos)
- [ ] Definir BatchSize explícito (ex: 50.000)
- [ ] Revisar nomenclatura do package (Romaneios vs. FichaViagem)
