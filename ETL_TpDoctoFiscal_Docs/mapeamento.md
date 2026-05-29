# ETL_TpDoctoFiscal — Mapeamento de Colunas

## Visão Geral

| Propriedade               | Valor              |
|---------------------------|--------------------|
| Data Flow                 | Tarefa Fluxo de Dados |
| Tabela Origem             | sistdf             |
| Tabela Destino            | dbo.tb_SERIE       |
| Colunas mapeadas          | 7                  |
| Colunas não mapeadas      | 1 (NrSerieERPSen)  |

---

## Mapeamento Completo

| # | Coluna Origem (sistdf) | Tipo SSIS     | CodePage | Tamanho | Coluna Destino (tb_SERIE) | Status      |
|---|------------------------|---------------|----------|---------|---------------------------|-------------|
| 1 | CdTpDoctoFiscal        | i4 (int)      | —        | —       | CdTpDoctoFiscal           | Mapeado     |
| 2 | DsTpDoctoFiscal        | str (varchar) | 1252     | 40      | DsTpDoctoFiscal           | Mapeado     |
| 3 | DsApelido              | str (varchar) | 1252     | 10      | DsApelido                 | Mapeado     |
| 4 | InTipoDocumento        | i4 (int)      | —        | —       | InTipoDocumento           | Mapeado     |
| 5 | NrSerie                | str (varchar) | 1252     | 3       | NrSerie                   | Mapeado     |
| 6 | NrModelo               | i4 (int)      | —        | —       | NrModelo                  | Mapeado     |
| 7 | InFormaImpressao       | i4 (int)      | —        | —       | InFormaImpressao          | Mapeado     |
| 8 | NrSerieERPSen          | str (varchar) | 1252     | 5       | (NrSerieERPSen — no schema) | NAO MAPEADO — NULL no destino |

---

## Distribuição por Tipo de Dado

```
i4 (inteiros)    ████████████████  4 colunas mapeadas  (57%)
str (texto)      ████████████      3 colunas mapeadas  (43%)
```

| Tipo SSIS | Quantidade | Percentual |
|-----------|------------|------------|
| i4        | 4          | 57%        |
| str       | 3          | 43%        |
| **Total** | **7**      | **100%**   |

---

## Distribuição por Grupo Semântico

| Grupo                | Colunas | Colunas                          |
|----------------------|---------|----------------------------------|
| Identificação        | 1       | CdTpDoctoFiscal                  |
| Descrição            | 2       | DsTpDoctoFiscal, DsApelido       |
| Flags/Indicadores    | 2       | InTipoDocumento, InFormaImpressao|
| Referência Fiscal    | 2       | NrSerie, NrModelo                |

---

## Coluna Perdida

| Coluna        | Tipo   | Tamanho | Observação                                              |
|---------------|--------|---------|----------------------------------------------------------|
| NrSerieERPSen | str    | 5       | Presente na origem (`sistdf`) e no schema da tabela destino, mas sem inputColumn — fica NULL |
