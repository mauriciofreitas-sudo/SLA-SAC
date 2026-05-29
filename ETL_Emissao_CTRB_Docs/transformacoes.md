# ETL_Emissao_CTRB — Transformacoes

## Visao Geral

O Data Flow e um passthrough direto da view de origem para a tabela de destino. Nao ha transformacoes intermediarias no SSIS — toda a logica de negocio esta encapsulada na view `vw_Base_Emissão_CTRB` no servidor de origem.

## Diagrama ASCII do Data Flow (Ativo)

```
+--------------------------------------------+
|            Origem OLE DB                   |
|  vw_Base_Emissão_CTRB                      |
|  (169.57.181.231 / softran_translute)      |
|  CommandTimeout: 300s                      |
+--------------------------------------------+
                    |
                    | Saída de Origem OLE DB (55 colunas)
                    v
+--------------------------------------------+
|          OLE DB Destination                |
|  [dbo].[f_base_emissao_ctrb]              |
|  (10.100.86.89 / DBStage)                 |
|  FastLoad: TABLOCK,CHECK_CONSTRAINTS      |
|  ErrorRowDisposition: RedirectRow         |
+--------------------------------------------+
```

## Diagrama ASCII do Data Flow (DESABILITADO — no Sequence Container)

```
+--------------------------------------------+
|            Origem OLE DB                   |
|  vw_Base_Emissão_CTRB (NOLOCK)            |
|  (SOFTRAN - TRANSLUTE / OLEDB)            |
|  DISABLED                                  |
+--------------------------------------------+
                    |
                    | Saida de Origem OLE DB (55 colunas)
                    v
+--------------------------------------------+
|          OLE DB Destination                |
|  [dbo].[f_base_emissao_ctrb]              |
|  DISABLED                                  |
+--------------------------------------------+
```

## Pre-processamento

```sql
-- Task "Delete Table" (DESABILITADA):
delete FROM [dbo].[stg_Base_Emissão_CTRB]
```

## Transformacoes no Data Flow

**Nenhuma transformacao aplicada no SSIS.** Fluxo 100% passthrough:
- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Sort / Aggregate
- A logica de negocio (CASE WHEN, JOINs, calculos de valores) esta na view de origem

## Mapeamento de Tipos de Dados

| Grupo | Coluna | Origem (str/1252) | Destino (wstr) | Observacao |
|---|---|---|---|---|
| Datas como string | DtInclusao | str(30) | wstr(30) | Data armazenada como string — RISCO |
| Datas como string | DtSaida | str(30) | wstr(30) | Data armazenada como string — RISCO |
| Datas como string | DtCTRB | str(30) | wstr(30) | Data armazenada como string — RISCO |
| Data real | DtEmissao | dbTimeStamp | dbTimeStamp | Correto |
| Valores financeiros | VlFreteCTRB | p38,s13 | p38,s4 | Precisao de escala reduzida no destino |
| Valores financeiros | VlAdiantamento | p38,s13 | p38,s4 | Precisao de escala reduzida no destino |
| Valores financeiros | VlPedagioCTRB | p38,s13 | p38,s4 | Precisao de escala reduzida no destino |
| Valores financeiros | VlAcrescimosFV | p38,s13 | p38,s13 | Mantido |
| Valores financeiros | VlDescontosFV | p38,s13 | p38,s13 | Mantido |
| Valores financeiros | VlCustoTransferencia | p38,s13 | p38,s6 | Escala diferente no destino |

## Caminhos dos Dados

```
Ativo: Package\Contêiner da Sequência\Tarefa Fluxo de Dados.Paths[Saída de Origem OLE DB]
  Start: Origem OLE DB.Outputs[Saída de Origem OLE DB]
  End:   OLE DB Destination.Inputs[OLE DB Destination Input]
```
