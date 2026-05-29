# ETL_Garantia_Receita — Transformacoes

## Visao Geral

O Data Flow `Tarefa Fluxo de Dados` e um passthrough direto da view de origem para a tabela de destino. Toda a logica de negocio esta encapsulada na view `vw_GARANTIA_RECEITA_V2` no servidor SOFTRAN.

## Diagrama ASCII do Data Flow

```
+--------------------------------------------+
|            Origem OLE DB                   |
|  vw_GARANTIA_RECEITA_V2 (NOLOCK)           |
|  (169.57.181.231 / softran_translute)      |
|  CommandTimeout: 0 (infinito)              |
+--------------------------------------------+
                    |
                    | Saída de Origem OLE DB (47 colunas)
                    v
+--------------------------------------------+
|           Destino OLE DB                   |
|  [dbo].[GARANTIA_RECEITA]                  |
|  (10.100.86.89 / DBStage)                  |
|  FastLoad: TABLOCK,CHECK_CONSTRAINTS       |
|  errorRowDisposition: FailComponent        |
+--------------------------------------------+
```

## Pre-processamento no Control Flow

```sql
-- Executado antes do Data Flow:
delete from [dbo].[GARANTIA_RECEITA]
```

## Transformacoes no Data Flow

**Nenhuma transformacao aplicada.** Fluxo 100% passthrough:
- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Sort / Aggregate
- Toda logica esta na view `vw_GARANTIA_RECEITA_V2`

## Mapeamento de Tipos de Dados

| Coluna | Tipo Origem | Tipo Destino (extMetadata) | Diferenca |
|---|---|---|---|
| GRIS | numeric p14,s4 | numeric p15,s2 | Precisao/escala diferentes |
| Pedágio | numeric p14,s4 | numeric p15,s2 | Precisao/escala diferentes |
| CTe Substituido | str 3 (cached) | str 5 (external) | Tamanho diferente origem vs destino |
| Origem UF | str 3 | str 3 | Coincide mas nome estranho para UF (deveria ser 2 chars) |
| Destino UF | str 3 | str 3 | Idem |
| Emissão | dbTimeStamp | dbTimeStamp | Correto |
| Dt Emissão Fatura | dbTimeStamp | dbTimeStamp | Correto |
| Dt Vencimento | dbTimeStamp | dbTimeStamp | Correto |
| Dt Pagamento | dbTimeStamp | dbTimeStamp | Correto |
| Dt Entrega | dbTimeStamp | dbTimeStamp | Correto |

## Caminho dos Dados

```
Path: Package\Contêiner da Sequência\Tarefa Fluxo de Dados.Paths[Saída de Origem OLE DB]
  Start: Origem OLE DB.Outputs[Saída de Origem OLE DB]
  End:   Destino OLE DB.Inputs[Entrada de Destino OLE DB]
```
