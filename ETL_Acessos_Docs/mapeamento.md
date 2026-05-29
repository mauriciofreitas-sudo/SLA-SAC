# Mapeamento de Colunas — ETL_Acessos

## Visão Geral

| Data Flow | Source | Destination | Transformações |
|-----------|--------|-------------|----------------|
| Load fPerf | vw_acesso_softran (5 col.) | fAcessos_Softran (6 col.) | Derived Column: +ExecutionDt |

## Mapeamento Completo — Data Flow: Load fPerf

| # | Coluna | Tipo SSIS | Tamanho | Origem | Grupo |
|---|--------|-----------|---------|--------|-------|
| 1 | Acesso | i4 (Int32) | — | vw_acesso_softran.Acesso | Identificação |
| 2 | DsUsuario | wstr | 10 | vw_acesso_softran.DsUsuario | Usuário |
| 3 | CdModulo | wstr | 3 | vw_acesso_softran.CdModulo | Módulo |
| 4 | UltimoAcesso | dbTimeStamp | — | vw_acesso_softran.UltimoAcesso | Temporal |
| 5 | Logon | dbTimeStamp | — | vw_acesso_softran.Logon | Temporal |
| 6 | ExecutionDt | date | — | @[User::ExecutionDateTime] via Derived Column | Auditoria ETL |

## Distribuição por Grupo

```
Identificação  [#] 1 coluna   |####################|  17%
Usuário        [#] 1 coluna   |####################|  17%
Módulo         [#] 1 coluna   |####################|  17%
Temporal       [#] 2 colunas  |########################################|  33%
Auditoria ETL  [#] 1 coluna   |####################|  17%
                                                          Total: 6 colunas
```

## Tipos de Dados — Resumo

| Tipo SSIS | Equivalente SQL Server | Colunas |
|-----------|----------------------|---------|
| i4 | INT | Acesso |
| wstr | NVARCHAR | DsUsuario, CdModulo |
| dbTimeStamp | DATETIME | UltimoAcesso, Logon |
| date | DATE | ExecutionDt |

## Colunas na Tabela Destino com Metadado Diferente (External)

| Coluna | Tipo produzido (SSIS) | Tipo externo registrado | Status |
|--------|----------------------|------------------------|--------|
| ExecutionDt | date | dbTimeStamp | Mismatch — ver issues_e_observacoes.md |
