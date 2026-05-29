# Issues e Observações — ETL_Base_Controladoria

## Resumo

| Severidade | Quantidade |
|------------|------------|
| 🔴 Critico | 3 |
| 🟠 Alto | 4 |
| 🟡 Medio | 4 |
| 🔵 Baixo | 3 |
| **Total** | **14** |

---

## Issues Identificados

### 🔴 ISSUE-001 — Datas armazenadas como STRING (wstr) em vez de dbTimeStamp

**Contexto:** As colunas `DtEmissao` e `DtSaida` sao do tipo `wstr` (tamanho 10) tanto na source quanto no destino. Isso indica que as datas estao sendo tratadas como texto (formato "YYYY-MM-DD").

**Impacto:**
- Nao e possivel fazer calculos de data (diferenca de dias, filtros por periodo, agregacoes mensais) sem conversao explicita
- Ordenacao por data funciona apenas se o formato for ISO (YYYY-MM-DD); outros formatos gerarao ordem errada
- Joins com tabelas de dimensao de tempo (dim_data) requerem CONVERT/CAST em toda query
- Power BI e outras ferramentas podem nao reconhecer as colunas como datas

**Correcao sugerida:**
1. Verificar se a view `VW_CONTROLADORIA_DE_FRETE` retorna as datas como string ou se sao convertidas na view
2. Adicionar uma transformacao Derived Column no SSIS para converter as strings em datas:
   ```
   (DT_DBDATE)(SUBSTRING(DtEmissao,1,4) + "-" + SUBSTRING(DtEmissao,6,2) + "-" + SUBSTRING(DtEmissao,9,2))
   ```
3. Alterar o tipo das colunas na tabela `fControladoria` para DATE ou DATETIME

---

### 🔴 ISSUE-002 — errorRowDisposition = IgnoreFailure na source e no destino

**Contexto:** A maioria das colunas na `Origem do ADO NET` tem `errorRowDisposition=IgnoreFailure`, e o destino `Destino do ADO NET` tambem usa `errorRowDisposition=IgnoreFailure` no input. Isso significa que erros de linha sao silenciosamente ignorados.

**Impacto:**
- Linhas com erro de conversao ou insercao sao descartadas sem nenhum aviso
- O package completa com sucesso mesmo que metade dos dados tenha sido perdida
- Impossivel saber quantos registros foram efetivamente carregados vs. ignorados

**Correcao sugerida:** Alterar para `FailComponent` (comportamento padrao correto) ou `RedirectRow` (redirecionar linhas com erro para um destino de log).

---

### 🔴 ISSUE-003 — ObjectName do package e "Package2" (nome padrao)

**Contexto:** O ObjectName interno do package e `Package2`, que e o nome padrao atribuido pelo SSIS ao criar um novo package. O arquivo se chama `ETL_Base_Controladoria.dtsx` mas internamente e `Package2`.

**Impacto:**
- Logs do SSIS Catalog, SQL Agent Jobs e reports mostram o nome "Package2", o que nao identifica o package
- Dificulta rastreamento de execucoes em ambientes com multiplos packages
- Indica que o package foi criado como copia/template sem renomeacao adequada

**Correcao sugerida:** Renomear o ObjectName para "ETL_Base_Controladoria" no SSIS Designer (Properties da raiz do package).

---

### 🟠 ISSUE-004 — Conexao `10.100.86.89 1` sem banco de dados especificado

**Contexto:** A conexao usada para o Execute SQL Task (`10.100.86.89 1`) tem a ConnectionString sem `Initial Catalog`:
```
Data Source=10.100.86.89;User ID=sqldba;Persist Security Info=True;Encrypt=True;...
```

**Impacto:** O SQL `DELETE FROM [DBStage].[dbo].[fControladoria]` usa nome qualificado de 3 partes, o que funciona mas depende da disponibilidade do banco `DBStage`. Se o banco padrao do usuario `sqldba` for diferente, queries sem qualificacao falham.

**Correcao sugerida:** Adicionar `Initial Catalog=DBStage` na ConnectionString da conexao `10.100.86.89 1`.

---

### 🟠 ISSUE-005 — Conexao ODBC `DBStage` declarada e nao utilizada

**Contexto:** Ha um quarto Connection Manager do tipo ODBC (`ADO.NET:System.Data.Odbc.OdbcConnection`) chamado `DBStage` usando `uid=sqldba;Dsn=DBStage;`. Este connection manager nao e referenciado por nenhum componente.

**Impacto:** Conexao orfã pode tentar conectar ao DSN ODBC durante validacao do package, causando erro se o DSN nao estiver configurado na maquina de execucao.

**Correcao sugerida:** Remover o Connection Manager ODBC `DBStage` do package.

---

### 🟠 ISSUE-006 — Precisao excessiva em colunas financeiras (38,13)

**Contexto:** As colunas `VlFreteCTRB`, `VlAdiantamento`, `VlPedagioCTRB` usam `numeric(38,13)` e `SaldoCTRB` usa `numeric(38,4)`. Precisao de 38 casas e escala de 13 sao extremamente altas para valores monetarios.

**Impacto:**
- Consumo de espaco desnecessario em disco e memoria
- Potencial impacto em performance de calculos
- Indica que a view de origem provavelmente faz calculos (ex.: divisoes) que resultam em muitas casas decimais, que deveriam ser arredondadas antes do destino

**Correcao sugerida:** Adicionar uma Derived Column para arredondar os valores antes do destino:
```
(DT_NUMERIC,14,4)(ROUND(VlFreteCTRB,4))
```

---

### 🟠 ISSUE-007 — Destino e uma VIEW (VW_CONTROLADORIA_BASE_EMISSAO), nao a tabela

**Contexto:** O destino ADO NET aponta para `"dbo"."VW_CONTROLADORIA_BASE_EMISSAO"`, que e uma view. O SSIS insere via view.

**Impacto:**
- A view precisa ser insertable (sem GROUP BY, DISTINCT, etc.) ou ter um INSTEAD OF INSERT trigger
- Se a view mudar de estrutura, o package falha sem aviso antecipado
- UseBulkInsertWhenPossible = true pode nao funcionar corretamente com views

**Correcao sugerida:** Verificar se a view e um alias simples de `fControladoria`. Se for, considerar inserir diretamente na tabela `fControladoria` para melhor performance e seguranca.

---

### 🟡 ISSUE-008 — isStoredProc=True no Execute SQL Task que nao usa stored procedure

**Contexto:** O Execute SQL Task "Limpar Tabela fControladoria" tem `SQLTask:IsStoredProc="True"` mas executa uma instrucao SQL direta (`DELETE FROM`), nao uma stored procedure.

**Impacto:** `IsStoredProc=True` altera como o SSIS processa o comando (usa `CommandType=StoredProcedure`). Para um DELETE simples, isso pode causar erros de sintaxe em alguns providers ou comportamentos inesperados.

**Correcao sugerida:** Alterar para `IsStoredProc="False"`.

---

### 🟡 ISSUE-009 — BypassPrepare=False no Execute SQL Task

**Contexto:** O Execute SQL Task tem `SQLTask:BypassPrepare="False"`, o que faz o SSIS preparar o statement antes de executar.

**Impacto:** Para um DELETE simples que nao usa parametros, `BypassPrepare=False` e desnecessario e pode causar um RTT extra ao servidor.

**Correcao sugerida:** Alterar para `BypassPrepare="True"` para statements simples sem parametros.

---

### 🟡 ISSUE-010 — Coluna `ID` com tamanho 1 (wstr(1)) — semantica ambigua

**Contexto:** A primeira coluna mapeada e `ID` com tipo `wstr(1)` (string de 1 caracter).

**Impacto:** Um campo chamado `ID` de tamanho 1 e incomum. Pode ser um indicador de tipo, status ou empresa codificado em 1 letra, mas o nome nao e descritivo. Dificulta a compreensao sem ver a view de origem.

**Correcao sugerida:** Verificar o significado de `ID` na view `VW_CONTROLADORIA_DE_FRETE` e renomear para algo mais descritivo (ex.: `TpRegistro`, `CdTipo`, etc.).

---

### 🟡 ISSUE-011 — Parametro com nome corrompido (encoding)

**Contexto:** O parametro do package se chama `ContInerDaSequNcia_DelayValidation` em vez de `ContêinerDaSequência_DelayValidation`. Os caracteres acentuados foram corrompidos.

**Impacto:** Referencia ao parametro `@[$Package::ContInerDaSequNcia_DelayValidation]` funciona pois o SSIS usa o nome corrompido, mas e confuso e indica problema de encoding no ambiente de desenvolvimento.

**Correcao sugerida:** Recriar o parametro com nome correto ou usar um nome sem acentos: `SequenceContainer_DelayValidation`.

---

### 🔵 ISSUE-012 — Nome do Data Flow Task e generico ("Data Flow Task")

**Contexto:** O Data Flow Task se chama simplesmente "Data Flow Task" (nome padrao).

**Impacto:** Dificulta leitura de logs. Em packages com multiplos data flows, nao e possivel identificar qual task falhou pelo nome.

**Correcao sugerida:** Renomear para "Load fControladoria" ou "Load VW_Controladoria_Base_Emissao".

---

### 🔵 ISSUE-013 — Error Output desconectado

**Contexto:** O error output do destino ADO NET nao esta conectado a nenhum destino de log.

**Impacto:** Combinado com `IgnoreFailure`, nao ha absolutamente nenhum registro de linhas rejeitadas.

**Correcao sugerida:** Conectar a um destino de log e mudar para `RedirectRow`.

---

### 🔵 ISSUE-014 — Conexao ODBC usa DSN — dependencia de configuracao de maquina

**Contexto:** A conexao `DBStage` (nao usada) usa DSN ODBC: `uid=sqldba;Dsn=DBStage;`. Isso significa que para funcionar, o DSN "DBStage" precisa estar configurado na maquina que executa o package.

**Impacto:** Em caso de failover ou mudanca de servidor de execucao, o DSN precisa ser reconfigurado manualmente.

**Correcao sugerida:** Como a conexao nao esta sendo usada, remover do package.

---

## Checklist de Melhoria

- [ ] Converter DtEmissao e DtSaida de wstr para dbTimeStamp (via Derived Column no SSIS)
- [ ] Alterar errorRowDisposition de IgnoreFailure para FailComponent ou RedirectRow
- [ ] Renomear ObjectName de "Package2" para "ETL_Base_Controladoria"
- [ ] Adicionar Initial Catalog=DBStage na conexao "10.100.86.89 1"
- [ ] Remover conexao ODBC "DBStage" nao utilizada
- [ ] Arredondar colunas numeric(38,13) para numeric(14,4) via Derived Column
- [ ] Verificar se destino VW_CONTROLADORIA_BASE_EMISSAO e insertable e considerar inserir na tabela diretamente
- [ ] Corrigir IsStoredProc=False no Execute SQL Task
- [ ] Alterar BypassPrepare=True no Execute SQL Task
- [ ] Investigar semantica da coluna ID (wstr(1))
- [ ] Recriar parametro com nome sem acentos corrompidos
- [ ] Renomear Data Flow Task para nome descritivo
- [ ] Conectar error output a tabela de log de erros
