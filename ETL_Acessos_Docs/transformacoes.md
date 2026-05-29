# Transformações — ETL_Acessos

## Resumo

O package possui **uma transformação** do tipo Derived Column que adiciona a coluna `ExecutionDt` ao fluxo de dados, derivada da variável de pacote `@[User::ExecutionDateTime]`.

## Componentes do Data Flow — Load fPerf

```
+------------------+      +------------------+      +----------------------+
|  ADO NET Source  |      |  Derived Column  |      |  ADO NET Destination |
|                  |      |                  |      |                      |
|  vw_acesso_      | ---> |  + ExecutionDt   | ---> |  fAcessos_Softran    |
|  softran         |      |  = @Execution    |      |                      |
|  (5 colunas)     |      |    DateTime      |      |  (6 colunas)         |
+------------------+      +------------------+      +----------------------+
      |                         |
      v (Error Output)          v (Error Output)
  [não conectado]          [não conectado]
```

### Path: ADO NET Source Output → Derived Column Input
- Passa as 5 colunas brutas da source para a transformação

### Path: Derived Column Output → ADO NET Destination Input
- Passa as 5 colunas + nova coluna `ExecutionDt` para o destino

## Transformação Derived Column — Detalhes

| Propriedade | Valor |
|-------------|-------|
| Nome da coluna nova | ExecutionDt |
| Tipo de saída | date |
| Expressão SSIS | `@[User::ExecutionDateTime]` |
| FriendlyExpression | `@[User::ExecutionDateTime]` |
| Ação | Nova coluna (não substitui existente) |

### Variável utilizada

| Variável | Namespace | Tipo | Definição |
|----------|-----------|------|-----------|
| ExecutionDateTime | User | DateTime | `GETDATE()` — avaliada no início da execução |

## Pré-processamento (SQL Tasks antes do Data Flow)

Não há Execute SQL Tasks neste package. O fluxo inicia diretamente com o Data Flow Task.

## Mapeamento de Tipos de Dados

| Coluna | Tipo na Source (SSIS) | Tipo no Destino (SSIS) | Conversão implícita |
|--------|----------------------|------------------------|---------------------|
| Acesso | i4 | i4 | Nenhuma |
| DsUsuario | wstr(10) | wstr(10) | Nenhuma |
| CdModulo | wstr(3) | wstr(3) | Nenhuma |
| UltimoAcesso | dbTimeStamp | dbTimeStamp | Nenhuma |
| Logon | dbTimeStamp | dbTimeStamp | Nenhuma |
| ExecutionDt | date (Derived Column) | dbTimeStamp (External) | Possível mismatch: `date` -> `dbTimeStamp` |

> Observação: O metadado externo da tabela `fAcessos_Softran` registra `ExecutionDt` como `dbTimeStamp`, mas a Derived Column produz `date`. O SSIS pode realizar conversão implícita sem erro, mas a parte de hora será sempre `00:00:00`.
