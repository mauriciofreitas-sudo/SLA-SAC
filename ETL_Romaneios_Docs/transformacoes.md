# ETL_Romaneios — Transformações

## Visão Geral

O package não utiliza componentes de transformação intermediários no Data Flow. Todos os 51 campos fluem diretamente da view de origem para a tabela destino (passthrough completo).

As transformações de negócio são realizadas internamente pela view `dbo.Ficha_de_Viagem_Geral` no servidor de origem.

## Diagrama ASCII — Data Flow

```
[ADO NET Source]
  |-- Origem: dbo.Ficha_de_Viagem_Geral (169.57.181.231)
  |-- Modo: TableOrViewName (sem SQL inline)
  |-- CommandTimeout: 30s
  |-- 51 colunas passadas diretamente
        |
        | (passthrough — sem transformações SSIS)
        v
[ADO NET Destination]
  |-- Destino: dbo.fFichaViagem (10.100.86.89 / DWGrupolc)
  |-- SqlBulkCopy habilitado
  |-- BatchSize: 0 (auto — buffer SSIS)
  |-- CommandTimeout: 30s
```

## Tipos de Dados — Consistência Origem x Destino

Todos os tipos são consistentes entre origem e destino:

| Grupo                     | Colunas                                                                                                          | Tipo              |
|---------------------------|------------------------------------------------------------------------------------------------------------------|-------------------|
| Inteiros (i4)             | EmpresaFicha, NrFicha, CdRotaRMS, EmpresaRomaneio, RotaRomaneio, NumeroCTRB, EmpresaCTE, NrDoctoFiscal         | i4                |
| Strings longas (wstr 100) | MotoristaFV, ProprietarioFV, DsRemetente, DsDestinatario, DsPagador                                            | wstr(100)         |
| Strings médias (wstr 40)  | MotoristaRmMan, ProprietarioRmMan, DsRedespacho                                                                  | wstr(40)          |
| Strings curtas (wstr ≤ 20)| VeiculoFV, CarretaFV, Carreta2FV, Carreta3FV, UserFicha, UserRomaneioManifesto, VeiculoRmMan, CarretaRmMan, Carreta2RmMan, Carreta3RmMan, UserCtrb, UserCTE, TipoDoctoFiscal, DsUFOrigem, DsUFDestino, CdRemetente, CdDestinatario, CdInscricao, CdRedespacho, RomaneioManifesto, CidadeColeta, CidadeEntrega | wstr(2-30) |
| Datas (dbTimeStamp)       | DtEmissao, DtEmissaoRmMan, DtEmissaoCTRB, DtEmissaoCTE                                                         | dbTimeStamp       |
| Decimal (numeric p=14 s=4)| VlComissao, ValorTotalCTRB, AdtoCTRB, PedagioCTRB, VlFretePeso, VlFreteValor, VlPedagio, VlGRIS, VlTotalPrestacao | numeric p=14s4 |

> Diferente do ETL_Painel_CTE, os campos de data usam `dbTimeStamp` (datetime nativo) — boa prática.

## Estrutura dos Dados por Grupo Semântico

### Dados da Ficha de Viagem (FV)
- EmpresaFicha, NrFicha, DtEmissao, VlComissao, CdRotaRMS
- MotoristaFV, ProprietarioFV, VeiculoFV, CarretaFV, Carreta2FV, Carreta3FV
- UserFicha

### Dados do Romaneio / Manifesto (RmMan)
- EmpresaRomaneio, RotaRomaneio, RomaneioManifesto
- UserRomaneioManifesto, DtEmissaoRmMan
- VeiculoRmMan, CarretaRmMan, Carreta2RmMan, Carreta3RmMan
- MotoristaRmMan, ProprietarioRmMan

### Dados do CTRB (Contrato de Transporte Rodoviário de Bens)
- NumeroCTRB, UserCtrb, DtEmissaoCTRB
- ValorTotalCTRB, AdtoCTRB, PedagioCTRB

### Dados do CT-e
- EmpresaCTE, NrDoctoFiscal, UserCTE, TipoDoctoFiscal, DtEmissaoCTE
- VlFretePeso, VlFreteValor, VlPedagio, VlGRIS, VlTotalPrestacao

### Dados Geográficos e de Partes
- CidadeColeta, DsUFOrigem, CidadeEntrega, DsUFDestino
- CdRemetente, DsRemetente, CdDestinatario, DsDestinatario
- CdInscricao, DsPagador, CdRedespacho, DsRedespacho
