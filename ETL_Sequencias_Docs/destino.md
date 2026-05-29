# ETL_Sequencias — Destino

## Servidor de Destino

| Propriedade            | Valor                      |
|------------------------|----------------------------|
| Servidor               | 10.100.86.89               |
| Banco de Dados         | DWGrupolc                  |
| Usuário                | sqldba                     |
| Tipo de Conexão        | ADO.NET (SqlClient)        |
| TrustServerCertificate | True                       |

---

## Destination Component: ADO NET Destination

| Propriedade               | Valor                     |
|---------------------------|---------------------------|
| Nome do Componente        | ADO NET Destination        |
| Tabela Destino            | `fFichasAbertas`          |
| BatchSize                 | 0 (usa buffer interno SSIS)|
| CommandTimeout            | 30 segundos               |
| UseBulkInsertWhenPossible | true                      |

---

## Estratégia de Carga

**TRUNCATE + INSERT (Full Reload)**

1. Task `Truncate fBaseSequencias` executa `TRUNCATE TABLE "fFichasAbertas"` via OLEDB (sqldba1)
2. Task `Load fBaseSequencias` insere todos os CTes filtrados (`DtCancelamento IS NULL AND cdremetente IS NOT NULL AND dtemissao >= '20240101'`)

---

## Schema da Tabela Destino `fFichasAbertas`

| # | Coluna                    | Tipo SSIS   | Tamanho / Precisão | Nulável |
|---|---------------------------|-------------|--------------------|---------|
| 1  | Empresa                  | wstr        | 23                 | Sim     |
| 2  | NrPlaca                  | wstr        | 8                  | Sim     |
| 3  | DtFechamento             | dbTimeStamp | —                  | Sim     |
| 4  | NrEvento                 | i8 (bigint) | —                  | Sim     |
| 5  | CdEmpresa                | i4          | —                  | Sim     |
| 6  | NrFicha                  | i4          | —                  | Sim     |
| 7  | DtEmissao                | dbTimeStamp | —                  | Sim     |
| 8  | RomaneioManifesto        | wstr        | 20                 | Sim     |
| 9  | CepDestinoManifesto      | i4          | —                  | Sim     |
| 10 | CidadeDestinoManifesto   | wstr        | 30                 | Sim     |
| 11 | UFDestinoManifesto       | wstr        | 2                  | Sim     |
| 12 | Tipo                     | wstr        | 9                  | Sim     |
| 13 | EmpresaCTE               | i4          | —                  | Sim     |
| 14 | Cdremetente              | wstr        | 14                 | Sim     |
| 15 | Remetente                | wstr        | 100                | Sim     |
| 16 | Destinatario             | wstr        | 14                 | Sim     |
| 17 | DsEntidade               | wstr        | 100                | Sim     |
| 18 | NumeroCTE                | i4          | —                  | Sim     |
| 19 | CepEntregaCTE            | i4          | —                  | Sim     |
| 20 | CidadeEntregaCTE         | wstr        | 30                 | Sim     |
| 21 | UFEntregaCTE             | wstr        | 2                  | Sim     |
| 22 | NrSeqControle            | i4          | —                  | Sim     |
| 23 | CdTpDoctoFiscal          | i4          | —                  | Sim     |
| 24 | DtPrevEntrega            | dbTimeStamp | —                  | Sim     |
| 25 | CTeDtEntrega             | dbTimeStamp | —                  | Sim     |
| 26 | DsUltimaOcorrencia       | wstr        | 30                 | Sim     |
| 27 | CodigoOcorrencia         | wstr        | 30                 | Sim     |
| 28 | Origem_COLETA            | i4          | —                  | Sim     |
| 29 | CidadeOrigemColeta       | wstr        | 30                 | Sim     |
| 30 | UFOrigemColeta           | wstr        | 2                  | Sim     |
| 31 | OrigemFicha              | r8          | —                  | Sim     |
| 32 | CidadeOrigemFicha        | wstr        | 30                 | Sim     |
| 33 | UFOrigemFicha            | wstr        | 2                  | Sim     |
| 34 | VlBaseCalculo            | numeric     | p14, s4            | Sim     |
| 35 | VlFretePeso              | numeric     | p14, s4            | Sim     |
| 36 | Qtpeso                   | numeric     | p14, s4            | Sim     |
| 37 | DtAgendamentoEntregaCTe  | dbTimeStamp | —                  | Sim     |

**Total: 37 colunas mapeadas**

---

## Configurações Adicionais

| Propriedade                | Valor                          |
|----------------------------|--------------------------------|
| errorRowDisposition        | FailComponent                  |
| Connection TRUNCATE        | OLEDB (`10.100.86.89.DWGrupolc.sqldba1`) |
| Connection INSERT          | ADO.NET (`10.100.86.89.DWGrupolc.sqldba`) |
