# Issues e Observações — ETL_Fichas

## Resumo

| Severidade | Quantidade |
|---|:---:|
| 🔴 Crítico | 1 |
| 🟠 Alto | 2 |
| 🟡 Médio | 3 |
| 🔵 Baixo | 2 |

---

## 🔴 Crítico

### [ISS-01] Variável `StartDate` declarada mas inutilizada

**Contexto:**  
O package declara a variável `User::StartDate = 01/01/2025` e a registra como parâmetro 0 no Execute SQL Task ("Truncate fFicha"). No entanto, o SQL configurado é:

```sql
DELETE FROM dbo.fFichaViagem
```

Não há `?` ou `@StartDate` no statement — o binding existe, mas o parâmetro não é consumido.

**Impacto:**  
O DELETE remove **todos** os registros da tabela a cada execução, independente da data. Se a intenção era realizar carga incremental (deletar e recarregar apenas a partir de 01/01/2025), isso **não está funcionando**.

**Hipóteses:**
- A intenção era incremental, mas o `WHERE` nunca foi adicionado → **bug**
- A intenção é sempre full-refresh e a variável é vestigial → pode remover a variável e o binding
- A variável foi planejada para uso futuro → documentar a intenção

**Correção se o objetivo for incremental:**

```sql
-- Para DELETE com filtro de data:
DELETE FROM dbo.fFichaViagem WHERE DtEmissao >= ?

-- E no source, usar SqlCommand mode com filtro equivalente:
SELECT * FROM dbo.Ficha_de_Viagem_Geral WHERE DtEmissao >= ?
```

---

## 🟠 Alto

### [ISS-02] 40 colunas no destino sem dados

**Contexto:**  
A tabela `dbo.fFichaViagem` possui 91 colunas. Este ETL carrega 51. As 40 restantes ficam sem dados após a carga:

Exemplos: `Status`, `NrDiasAtraso`, `CTeDtEntrega`, `DtPrevisaoEntregaCTe`, `NrRegistros`, `CNPJRemetente`, campos de NF (`NFNr`, `NFVlr`), campos de CT-e ampliados.

**Impacto:**  
- Consultas que dependem dessas colunas retornam `NULL`
- Se as colunas têm constraint `NOT NULL`, a carga falha
- Pode indicar que outros ETLs dependem desta carga para depois complementar (risco de ordem de execução)

**Ação recomendada:**  
Verificar se existe outro package ETL que preenche essas colunas após este. Se sim, documentar a dependência de ordem.

---

### [ISS-03] Error Output desconectado em ambos os componentes

**Contexto:**  
Tanto o ADO NET Source quanto o ADO NET Destination têm um Error Output configurado, mas **nenhum está conectado** a um handler (arquivo de log, tabela de erros, etc.).

**Impacto:**  
- Qualquer falha de conversão ou inserção encerra o pipeline abruptamente
- Não há registro de qual registro causou a falha
- Sem possibilidade de reprocessamento seletivo

**Correção sugerida:**  
Conectar o error output a um destino de arquivo flat ou tabela de erros:

```
ADO NET Source Error Output ──► [Flat File / Tabela dbo.fFichaViagem_Erros]
ADO NET Destination Error Output ──► [Flat File / Tabela dbo.fFichaViagem_Erros]
```

---

## 🟡 Médio

### [ISS-04] Duas conexões redundantes ao mesmo servidor de destino

**Contexto:**  
O package mantém duas connection managers apontando para o mesmo servidor/banco/usuário:

| ID | Tipo | Servidor | Banco | Usuário |
|---|---|---|---|---|
| `sqldba1` | OLE DB (SQLNCLI11.1) | 10.100.86.89 | DWGrupolc | sqldba |
| `datalc` (DW) | ADO.NET (SqlClient) | 10.100.86.89 | DWGrupolc | sqldba |

`sqldba1` é usado apenas no Execute SQL Task (DELETE). `datalc` é usado no Data Flow (INSERT).

**Impacto:**  
Dois conjuntos de credenciais para gerenciar. Se a senha mudar, precisa ser atualizada em dois lugares.

**Correção sugerida:**  
Usar a mesma conexão ADO.NET (`datalc`) para o Execute SQL Task também — ela suporta comandos SQL diretos.

---

### [ISS-05] `validateExternalMetadata="False"` no Source

**Contexto:**  
O componente ADO NET Source tem a validação de metadados externos desabilitada.

**Impacto:**  
Alterações no schema da view `dbo.Ficha_de_Viagem_Geral` (renomeação de colunas, mudança de tipo, remoção de colunas) não são detectadas em design-time. O package só falha em runtime.

**Ação recomendada:**  
Habilitar a validação (`validateExternalMetadata="True"`) e sincronizar os metadados periodicamente com "Refresh" no SSIS Designer.

---

### [ISS-06] Timeout assimétrico entre Source e Destination

**Contexto:**  

| Componente | Timeout |
|---|---|
| ADO NET Source | 600 segundos (10 min) |
| ADO NET Destination | **30 segundos** |

**Impacto:**  
Se o volume de dados for grande, o INSERT em bulk pode ultrapassar 30 segundos e causar timeout no destino, mesmo que a leitura da origem tenha funcionado bem.

**Correção sugerida:**  
Alinhar o timeout do destino com o da origem (600s) ou definir 0 para infinito.

---

## 🔵 Baixo

### [ISS-07] Task nomeada "Truncate" mas usa DELETE

**Contexto:**  
A task se chama "Truncate fFicha" mas executa `DELETE FROM dbo.fFichaViagem`, não `TRUNCATE TABLE`.

**Impacto:**  
`DELETE` sem `WHERE` é funcionalmente equivalente ao `TRUNCATE` para o resultado final, mas é mais lento e gera mais log de transação (especialmente em tabelas grandes), pois deleta linha a linha no log.

**Correção sugerida (se não houver FK apontando para a tabela):**

```sql
TRUNCATE TABLE dbo.fFichaViagem
```

> `TRUNCATE` não pode ser usado se houver Foreign Keys referenciando a tabela. Verifique antes de alterar.

---

### [ISS-08] Sem registro de auditoria de carga

**Contexto:**  
O package não registra:
- Data/hora de início e fim
- Quantidade de registros processados
- Sucesso ou falha

**Impacto:**  
Sem histórico de execução além do que o SQL Server Agent eventualmente registra.

**Sugestão:**  
Adicionar uma tabela de log de execução e tasks de INSERT no início e fim do package:

```sql
-- Exemplo de estrutura
CREATE TABLE dbo.LogETL (
    IdLog       int IDENTITY PRIMARY KEY,
    NmPackage   varchar(100),
    DtInicio    datetime,
    DtFim       datetime,
    NrRegistros int,
    DsStatus    varchar(20),
    DsMensagem  varchar(500)
)
```

---

## Checklist de Melhoria

- [ ] Definir se a carga é full-refresh ou incremental e corrigir o SQL de acordo
- [ ] Conectar error outputs a um destino de log
- [ ] Remover/consolidar a conexão OLE DB redundante
- [ ] Habilitar `validateExternalMetadata`
- [ ] Alinhar timeouts Source/Destination
- [ ] Verificar se 40 colunas NULL dependem de outro ETL (e documentar a ordem)
- [ ] Avaliar substituição de `DELETE` por `TRUNCATE`
- [ ] Implementar log de auditoria de carga
