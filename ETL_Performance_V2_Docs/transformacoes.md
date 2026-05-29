# ETL_Performance_V2 — Transformações

## Visão Geral

O package **não possui componentes de transformação** no Data Flow. Todos os dados fluem diretamente da view de origem para a tabela destino (passthrough completo).

As transformações de negócio são realizadas internamente pela view `dbo.vw_fPerformance` no servidor de origem, que não está exposta no arquivo SSIS.

## Diagrama ASCII — Data Flow

```
[ADO NET Source]
  |-- Origem: dbo.vw_fPerformance (169.57.181.231)
  |-- Modo: TableOrViewName (sem SQL inline)
  |-- CommandTimeout: 30s
  |-- 43 colunas passadas diretamente
        |
        | (passthrough — sem transformações SSIS)
        v
[ADO NET Destination]
  |-- Destino: dbo.fPerformance (10.100.86.89 / DBStage)
  |-- SqlBulkCopy habilitado
  |-- BatchSize: 50.000
  |-- CommandTimeout: 30s
```

## Fluxo de Controle — Task GetDate 1M (Desabilitada)

Existe uma task SQL desabilitada que calcularia a data de início:

```sql
SELECT DATEADD(month, DATEDIFF(month, 0, GETDATE()) - 1, 0) AS StartDate
```

- Resultado seria gravado na variável `User::StartDate`
- Como está desabilitada, `User::StartDate` mantém o valor padrão `1/1/2025`

## DELETE Sem Filtro Efetivo

A task "Truncate fPerf" usa:

```sql
DELETE FROM dbo.fPerformance
```

Apesar de ter `User::StartDate` vinculado como parâmetro (`ParameterName="0"`), **não há cláusula WHERE** na query que consuma o parâmetro.

**Efeito:** DELETE total da tabela `dbo.fPerformance` a cada execução.

## Tipos de Dados — Consistência Origem x Destino

Todos os 43 campos têm tipos consistentes entre origem e destino:

| Grupo           | Colunas                                                   | Tipo              |
|-----------------|-----------------------------------------------------------|-------------------|
| Inteiros (i4)   | NrDiasAtraso, NFNr, CTeEmpresa, CTeNr, TipoDoctoFiscal, NrSeqControle, NaturezaCod, HistEntregaCod, CdGrupoCliente, NrRegistros | i4 |
| Strings (wstr)  | Status, FichaViagem, IDCTEPERF, CNPJRemetente, NomeRemetente, NFSerie, CNPJDestinatario, NomeDestinatario, CidadeDestino, UFDestino, CNPJPagador, NomePagador, NaturezaNome, TipoTransporte, HistEntregaNome, FichaVUltManifesto, FichaVUltRom, TipoEmissao, DsGrupoCliente, DsRedespacho, CdRedespacho | wstr |
| Datas (ts)      | DtPrevisaoEntregaCTeNova, Hora, NFDtEmissao, CTeDtEmissao, DtPrevisaoEntregaCTe, DtAgendamentoEntregaCTe, CTeDtEntrega, DtRomaneio, DtManifesto | dbTimeStamp |
| Decimal         | NFVlr, Volumes, QtPesoCubado                              | numeric p=14 s=4  |

> Diferente do ETL_Painel_CTE, este package usa tipos nativos datetime (dbTimeStamp) para as colunas de data — boa prática.
