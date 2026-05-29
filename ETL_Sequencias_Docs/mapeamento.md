# ETL_Sequencias — Mapeamento de Colunas

## Visão Geral

| Propriedade              | Valor                              |
|--------------------------|------------------------------------|
| Data Flow                | Load fBaseSequencias               |
| Tabela/View Origem       | gtcconhe (+ 20+ tabelas SOFTRAN)   |
| Destino                  | fFichasAbertas                     |
| Total de colunas mapeadas | 37                                |

---

## Mapeamento Completo

| # | Coluna Destino            | Tipo SSIS   | Tamanho   | Grupo Semântico     |
|---|---------------------------|-------------|-----------|---------------------|
| 1  | Empresa                  | wstr        | 23        | Identificação       |
| 2  | NrPlaca                  | wstr        | 8         | Veículo             |
| 3  | DtFechamento             | dbTimeStamp | —         | Temporal            |
| 4  | NrEvento                 | i8          | —         | Identificação       |
| 5  | CdEmpresa                | i4          | —         | Identificação       |
| 6  | NrFicha                  | i4          | —         | Identificação       |
| 7  | DtEmissao                | dbTimeStamp | —         | Temporal            |
| 8  | RomaneioManifesto        | wstr        | 20        | Romaneio/Manifesto  |
| 9  | CepDestinoManifesto      | i4          | —         | Geografico          |
| 10 | CidadeDestinoManifesto   | wstr        | 30        | Geografico          |
| 11 | UFDestinoManifesto       | wstr        | 2         | Geografico          |
| 12 | Tipo                     | wstr        | 9         | CTe                 |
| 13 | EmpresaCTE               | i4          | —         | CTe                 |
| 14 | Cdremetente              | wstr        | 14        | Partes              |
| 15 | Remetente                | wstr        | 100       | Partes              |
| 16 | Destinatario             | wstr        | 14        | Partes              |
| 17 | DsEntidade               | wstr        | 100       | Partes              |
| 18 | NumeroCTE                | i4          | —         | CTe                 |
| 19 | CepEntregaCTE            | i4          | —         | Geografico          |
| 20 | CidadeEntregaCTE         | wstr        | 30        | Geografico          |
| 21 | UFEntregaCTE             | wstr        | 2         | Geografico          |
| 22 | NrSeqControle            | i4          | —         | CTe                 |
| 23 | CdTpDoctoFiscal          | i4          | —         | CTe                 |
| 24 | DtPrevEntrega            | dbTimeStamp | —         | Temporal            |
| 25 | CTeDtEntrega             | dbTimeStamp | —         | Temporal            |
| 26 | DsUltimaOcorrencia       | wstr        | 30        | Ocorrência          |
| 27 | CodigoOcorrencia         | wstr        | 30        | Ocorrência          |
| 28 | Origem_COLETA            | i4          | —         | Geografico          |
| 29 | CidadeOrigemColeta       | wstr        | 30        | Geografico          |
| 30 | UFOrigemColeta           | wstr        | 2         | Geografico          |
| 31 | OrigemFicha              | r8          | —         | Identificação       |
| 32 | CidadeOrigemFicha        | wstr        | 30        | Geografico          |
| 33 | UFOrigemFicha            | wstr        | 2         | Geografico          |
| 34 | VlBaseCalculo            | numeric     | p14, s4   | Valores             |
| 35 | VlFretePeso              | numeric     | p14, s4   | Valores             |
| 36 | Qtpeso                   | numeric     | p14, s4   | Valores             |
| 37 | DtAgendamentoEntregaCTe  | dbTimeStamp | —         | Temporal            |

---

## Distribuição por Tipo de Dado

```
wstr (texto)        ██████████████████████  17 colunas  (46%)
i4 (inteiro)        ████████████            11 colunas  (30%)
dbTimeStamp (data)  ██████                  6 colunas   (16%)
numeric (decimal)   ██                      3 colunas   (8%)
i8 (bigint)         █                       1 coluna    (3%)
r8 (double)         █                       1 coluna    (3%)
```

| Tipo SSIS   | Quantidade | Percentual |
|-------------|------------|------------|
| wstr        | 17         | 46%        |
| i4          | 11         | 30%        |
| dbTimeStamp | 6          | 16%        |
| numeric     | 3          | 8%         |
| i8          | 1          | 3%         |
| r8          | 1          | 3%         |
| **Total**   | **37**     | **100%**   |

## Distribuição por Grupo Semântico

| Grupo              | Colunas |
|--------------------|---------|
| Geografico         | 12      |
| CTe                | 6       |
| Partes             | 4       |
| Temporal           | 6       |
| Identificação      | 4       |
| Valores            | 3       |
| Ocorrência         | 2       |
| Veículo            | 1       |
| Romaneio/Manifesto | 1       |
