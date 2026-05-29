# ETL_Ordem_Compra — Issues e Observacoes

## Resumo de Severidades

| Severidade | Quantidade |
|---|---|
| CRITICO | 1 |
| ALTO | 3 |
| MEDIO | 2 |
| BAIXO | 2 |
| **Total** | **8** |

## Issues Encontrados

### CRITICO-01 — ConnectionManager "DWGrupolc" aponta para DBStage (banco errado)
**Descricao:** O ConnectionManager `10.100.86.89.DWGrupolc.sqldba` (ID {E126819E}) tem ObjectName "DWGrupolc" mas a connection string contem `Initial Catalog=DBStage` — o banco real e DBStage, nao DWGrupolc.
**Impacto:** Nome enganoso — quem ler a configuracao pensa que o destino e o DW (Data Warehouse), quando na verdade e o staging. Potencial para deploy em ambiente errado.
**Recomendacao:** Corrigir o ObjectName para `10.100.86.89.DBStage.sqldba` ou corrigir o Initial Catalog para `DWGrupolc` se for intencional.

### ALTO-01 — Typo no nome da coluna: FORNECDOR em vez de FORNECEDOR
**Descricao:** A coluna 13 se chama `FORNECDOR` (faltando a letra E). Este typo existe tanto no source (view) quanto no destino (tabela).
**Impacto:** Prejudica legibilidade do schema, dificulta joins com outras tabelas que possam ter o campo com nome correto.
**Recomendacao:** Corrigir na view `vw_Ordens_Compras` e na tabela `stg_Ordens_Compras`, com ALTER TABLE / EXEC sp_rename.

### ALTO-02 — Saida de erro ADO.NET desconectada
**Descricao:** O `ADO NET Destination Error Output` e o `ADO NET Source Error Output` nao estao conectados a nenhum destino de log.
**Impacto:** Linhas rejeitadas sao perdidas silenciosamente.
**Recomendacao:** Redirecionar para tabela de erros com informacoes de contexto.

### ALTO-03 — Assimetria de timeout: Source=600s, Destination=30s
**Descricao:** O ADO NET Source tem CommandTimeout=600s e o ADO NET Destination tem CommandTimeout=30s.
**Impacto:** Se o volume de dados for grande, o insert pode expirar em 30 segundos enquanto o source ainda esta processando — causando falha parcial.
**Recomendacao:** Aumentar o timeout do destino para pelo menos 300s ou 0 (infinito) para cargas bulk.

### MEDIO-01 — Conexao {6BB7A8D8} (DWGrupolc OLEDB) nao e usada
**Descricao:** O ConnectionManager `10.100.86.89.DWGrupolc.sqldba1` (OLEDB para DWGrupolc) esta declarado mas nao e referenciado em nenhuma task.
**Impacto:** Conexao ociosa que testa conectividade desnecessariamente na validacao do package.
**Recomendacao:** Remover o ConnectionManager nao utilizado.

### MEDIO-02 — Escala 255 em campos monetarios no External Metadata
**Descricao:** O metadata externo registra `scale=255` para VL_UNIT, VL_TOTAL e VlTotalRecebimento. Embora nao cause falha de execucao (o ADO.NET faz conversao implicita), indica descasamento entre metadata do SSIS e schema real.
**Impacto:** Pode causar avisos de validacao e confusao em manutencao.
**Recomendacao:** Atualizar os metadados externos com "Refresh" do componente.

### BAIXO-01 — Sem log de auditoria
**Descricao:** Nao ha registro de linhas processadas, hora de inicio/fim ou status.
**Recomendacao:** Adicionar task de log apos o Data Flow.

### BAIXO-02 — validateExternalMetadata = false no Source
**Descricao:** O ADO NET Source tem `validateExternalMetadata="False"`. Isso significa que o SSIS nao valida o schema da view em tempo de design/validacao.
**Impacto:** Mudancas no schema da view (adicao/remocao de colunas) passam despercebidas ate a execucao.
**Recomendacao:** Habilitar validacao e manter metadata sincronizado.

## Checklist de Melhoria

- [ ] Corrigir banco no ConnectionManager DWGrupolc (DBStage vs DWGrupolc)
- [ ] Corrigir typo FORNECDOR -> FORNECEDOR na view e na tabela
- [ ] Conectar saidas de erro ADO.NET a destino de log
- [ ] Aumentar CommandTimeout do Destination (30s -> 300s ou 0)
- [ ] Remover ConnectionManager {6BB7A8D8} nao utilizado
- [ ] Atualizar external metadata para corrigir scale=255
- [ ] Implementar log de auditoria
- [ ] Habilitar validateExternalMetadata no ADO NET Source
