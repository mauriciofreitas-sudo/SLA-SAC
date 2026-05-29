# ETL_Garantia_Receita2 — Issues e Observacoes

## Tabela de Severidades

| Simbolo | Nivel | Descricao |
|---|---|---|
| 🔴 | Critico | Pode causar falha em producao ou perda de dados |
| 🟠 | Alto | Risco operacional significativo ou dado incorreto |
| 🟡 | Medio | Problema de qualidade ou manutencao |
| 🔵 | Baixo | Sugestao de melhoria ou padrao |

## Issues Encontrados

### 🔴 ISSUE-01: Mudanca de tipo em "Tipo de Documento" — incompatibilidade com v1

| Campo | Detalhe |
|---|---|
| Coluna | Tipo de Documento |
| Origem | i4 (inteiro) |
| Destino (external) | wstr(40) — string de 40 chars |
| Problema | O tipo mudou de inteiro para string entre a origem e o destino. O SSIS usa `AllowImplicitStringConversion=true` para converter silenciosamente, o que pode gerar problemas se a tabela destino tiver a coluna definida como INT |
| Risco | Se a tabela GARANTIA_RECEITA ainda tiver Tipo de Documento como INT, o package falhara em runtime. Se o DDL da tabela ja foi alterado para VARCHAR, o v1 (ETL_Garantia_Receita) pode estar incompativel |
| Recomendacao | Verificar DDL atual da tabela GARANTIA_RECEITA; documentar qual version (v1 ou v2) esta ativo em producao |

### 🟠 ISSUE-02: Inconsistencia de precisao/escala em colunas financeiras

| Campo | Detalhe |
|---|---|
| Colunas | Total Prestação, Total Frete, Valor Pago, Acréscimos, Deduções, Saldo |
| Origem | numeric p14,s4 |
| Destino (external) | numeric p15,s2 |
| Problema | A origem traz valores com 4 casas decimais, mas o schema do destino so suporta 2. Conversao silenciosa com possivel arredondamento |
| Risco | Perda de precisao em valores financeiros (centesimos de centavo) |
| Recomendacao | Alinhar escala para p14,s4 em toda a cadeia (origem, destino e tabela SQL) |

### 🟠 ISSUE-03: Valor Total da Fatura com precisao excessiva na origem

| Campo | Detalhe |
|---|---|
| Coluna | Valor Total da Fatura |
| Origem | numeric p38,s4 |
| Destino | numeric p15,s2 |
| Problema | Precision 38 na origem vs 15 no destino — reducao drastica de precisao |
| Risco | Valores de fatura muito grandes (acima de 10^13) podem ser truncados silenciosamente |
| Recomendacao | Padronizar para numeric p15,s2 em toda a cadeia ou documentar o range esperado |

### 🟠 ISSUE-04: Coexistencia de dois packages com mesmo objetivo (v1 e v2)

| Campo | Detalhe |
|---|---|
| Packages | ETL_Garantia_Receita + ETL_Garantia_Receita2 |
| Problema | Dois packages carregam a mesma tabela GARANTIA_RECEITA. Qual esta ativo em producao? Se ambos forem agendados, a tabela pode ser truncada e recarregada duas vezes (v1 apaga o que v2 carregou) |
| Risco | Dado duplo ou dado perdido dependendo da ordem de execucao |
| Recomendacao | Desativar/arquivar o v1 (ETL_Garantia_Receita) e padronizar no v2. Documentar o motivo de existirem dois packages |

### 🟡 ISSUE-05: Error Output desconectado (origem e destino)

| Campo | Detalhe |
|---|---|
| Componentes | ADO NET Source (Error Output) e ADO NET Destination (Error Output) |
| Problema | Ambos os error outputs estao definidos mas nao conectados a nenhum destino de log |
| Risco | Linhas rejeitadas sao perdidas sem rastreabilidade |
| Recomendacao | Conectar a tabela de rejeicoes ou arquivo de log |

### 🟡 ISSUE-06: validateExternalMetadata = False no destino

| Campo | Detalhe |
|---|---|
| Componente | ADO NET Destination |
| Problema | Validacao de metadados externos desabilitada |
| Risco | Incompatibilidades de schema so detectadas em runtime |
| Recomendacao | Habilitar apos estabilizar schema da tabela GARANTIA_RECEITA |

### 🟡 ISSUE-07: Nome do Data Flow ("Load fCTE") semanticamente inadequado

| Campo | Detalhe |
|---|---|
| Task | Load fCTE |
| Problema | O nome sugere que o Data Flow carrega dados de CTe especificamente, mas na realidade carrega a tabela GARANTIA_RECEITA. Nome enganoso para quem mantém o package |
| Recomendacao | Renomear para "Load GARANTIA_RECEITA" para consistencia com o proposito |

### 🔵 ISSUE-08: Ausencia de auditoria de execucao

| Campo | Detalhe |
|---|---|
| Problema | Nao ha variaveis de auditoria (data execucao, linhas carregadas, duracao) |
| Recomendacao | Adicionar tabela de controle de carga ou SSIS Logging |

## Melhorias em relacao ao ETL_Garantia_Receita (v1)

| Item | v1 | v2 | Avaliacao |
|---|---|---|---|
| Pre-processamento | DELETE (lento) | TRUNCATE (rapido) | Melhoria |
| Provider destino | SQLOLEDB.1 (legado) | ADO.NET (moderno) | Melhoria |
| Coluna CdTitulo | Ausente | Presente | Melhoria |
| UF tamanho | str(3) incorreto | wstr(2) correto | Melhoria |
| Unicode | ANSI/1252 | Unicode (wstr) | Melhoria |
| Connection managers | 3 (inclui sem catalog) | 3 (bem definidas) | Melhoria |
| Seguranca | DontSaveSensitive | EncryptSensitiveWithKey | Melhoria |
| Tipo de Documento | i4 consistente | i4→wstr (conflito) | Regressao |
| Escala financeira | p14,s4 consistente | p14→p15,s2 (conflito) | Regressao |

## Checklist de Melhorias

- [ ] Verificar e documentar qual package (v1 ou v2) esta ativo em producao
- [ ] Desativar ETL_Garantia_Receita (v1) para evitar conflito
- [ ] Verificar DDL atual de GARANTIA_RECEITA para Tipo de Documento (INT vs VARCHAR)
- [ ] Alinhar precisao de todas as colunas monetarias (p14,s4 em toda a cadeia)
- [ ] Conectar error outputs a tabela de rejeicoes
- [ ] Habilitar validateExternalMetadata
- [ ] Renomear Data Flow de "Load fCTE" para "Load GARANTIA_RECEITA"
- [ ] Adicionar auditoria de execucao (data, linhas, duracao)
- [ ] Avaliar adicionar filtro de data na view (DtEmissao >= '20240101' como no ETL_Garantia_Receita2 SQL)
