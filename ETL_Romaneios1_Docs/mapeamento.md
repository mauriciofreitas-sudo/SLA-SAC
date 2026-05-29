# ETL_Romaneios1 — Mapeamento de Colunas

## Visão Geral

| Propriedade        | Valor                        |
|--------------------|------------------------------|
| Data Flow          | Load fFicha                  |
| Origem             | dbo.Ficha_de_Viagem_Geral    |
| Destino            | dbo.fFichaViagem             |
| Total de colunas mapeadas | 51                   |
| Colunas não mapeadas na dest. | 40               |

---

## Mapeamento Completo

| # | Coluna Origem (Source)    | Tipo SSIS      | Tamanho      | Coluna Destino (Dest)     | Grupo Semântico      |
|---|---------------------------|----------------|--------------|---------------------------|----------------------|
| 1  | EmpresaFicha             | i4             | —            | EmpresaFicha              | Ficha de Viagem      |
| 2  | NrFicha                  | i4             | —            | NrFicha                   | Ficha de Viagem      |
| 3  | DtEmissao                | dbTimeStamp    | —            | DtEmissao                 | Ficha de Viagem      |
| 4  | VlComissao               | numeric        | p14,s4       | VlComissao                | Ficha de Viagem      |
| 5  | CdRotaRMS                | i4             | —            | CdRotaRMS                 | Ficha de Viagem      |
| 6  | MotoristaFV              | wstr           | 100          | MotoristaFV               | Ficha de Viagem      |
| 7  | ProprietarioFV           | wstr           | 100          | ProprietarioFV            | Ficha de Viagem      |
| 8  | VeiculoFV                | wstr           | 8            | VeiculoFV                 | Ficha de Viagem      |
| 9  | CarretaFV                | wstr           | 8            | CarretaFV                 | Ficha de Viagem      |
| 10 | Carreta2FV               | wstr           | 8            | Carreta2FV                | Ficha de Viagem      |
| 11 | Carreta3FV               | wstr           | 8            | Carreta3FV                | Ficha de Viagem      |
| 12 | UserFicha                | wstr           | 10           | UserFicha                 | Ficha de Viagem      |
| 13 | EmpresaRomaneio          | i4             | —            | EmpresaRomaneio           | Romaneio/Manifesto   |
| 14 | RotaRomaneio             | i4             | —            | RotaRomaneio              | Romaneio/Manifesto   |
| 15 | RomaneioManifesto        | wstr           | 20           | RomaneioManifesto         | Romaneio/Manifesto   |
| 16 | UserRomaneioManifesto    | wstr           | 10           | UserRomaneioManifesto     | Romaneio/Manifesto   |
| 17 | DtEmissaoRmMan           | dbTimeStamp    | —            | DtEmissaoRmMan            | Romaneio/Manifesto   |
| 18 | VeiculoRmMan             | wstr           | 8            | VeiculoRmMan              | Romaneio/Manifesto   |
| 19 | CarretaRmMan             | wstr           | 8            | CarretaRmMan              | Romaneio/Manifesto   |
| 20 | Carreta2RmMan            | wstr           | 8            | Carreta2RmMan             | Romaneio/Manifesto   |
| 21 | Carreta3RmMan            | wstr           | 8            | Carreta3RmMan             | Romaneio/Manifesto   |
| 22 | MotoristaRmMan           | wstr           | 40           | MotoristaRmMan            | Romaneio/Manifesto   |
| 23 | ProprietarioRmMan        | wstr           | 40           | ProprietarioRmMan         | Romaneio/Manifesto   |
| 24 | NumeroCTRB               | i4             | —            | NumeroCTRB                | CTRB                 |
| 25 | UserCtrb                 | wstr           | 10           | UserCtrb                  | CTRB                 |
| 26 | DtEmissaoCTRB            | dbTimeStamp    | —            | DtEmissaoCTRB             | CTRB                 |
| 27 | ValorTotalCTRB           | numeric        | p14,s4       | ValorTotalCTRB            | CTRB                 |
| 28 | AdtoCTRB                 | numeric        | p14,s4       | AdtoCTRB                  | CTRB                 |
| 29 | PedagioCTRB              | numeric        | p14,s4       | PedagioCTRB               | CTRB                 |
| 30 | EmpresaCTE               | i4             | —            | EmpresaCTE                | CTe                  |
| 31 | VlFretePeso              | numeric        | p14,s4       | VlFretePeso               | CTe / Valores        |
| 32 | VlFreteValor             | numeric        | p14,s4       | VlFreteValor              | CTe / Valores        |
| 33 | VlPedagio                | numeric        | p14,s4       | VlPedagio                 | CTe / Valores        |
| 34 | VlGRIS                   | numeric        | p14,s4       | VlGRIS                    | CTe / Valores        |
| 35 | VlTotalPrestacao         | numeric        | p14,s4       | VlTotalPrestacao          | CTe / Valores        |
| 36 | NrDoctoFiscal            | i4             | —            | NrDoctoFiscal             | CTe                  |
| 37 | UserCTE                  | wstr           | 10           | UserCTE                   | CTe                  |
| 38 | TipoDoctoFiscal          | wstr           | 10           | TipoDoctoFiscal           | CTe                  |
| 39 | DtEmissaoCTE             | dbTimeStamp    | —            | DtEmissaoCTE              | CTe                  |
| 40 | CidadeColeta             | wstr           | 30           | CidadeColeta              | Geografico           |
| 41 | DsUFOrigem               | wstr           | 2            | DsUFOrigem                | Geografico           |
| 42 | CidadeEntrega            | wstr           | 30           | CidadeEntrega             | Geografico           |
| 43 | DsUFDestino              | wstr           | 2            | DsUFDestino               | Geografico           |
| 44 | CdRemetente              | wstr           | 14           | CdRemetente               | Partes               |
| 45 | DsRemetente              | wstr           | 100          | DsRemetente               | Partes               |
| 46 | CdDestinatario           | wstr           | 14           | CdDestinatario            | Partes               |
| 47 | DsDestinatario           | wstr           | 100          | DsDestinatario            | Partes               |
| 48 | CdInscricao              | wstr           | 14           | CdInscricao               | Partes               |
| 49 | DsPagador                | wstr           | 100          | DsPagador                 | Partes               |
| 50 | CdRedespacho             | wstr           | 14           | CdRedespacho              | Partes               |
| 51 | DsRedespacho             | wstr           | 40           | DsRedespacho              | Partes               |

---

## Distribuição por Tipo de Dado

```
i4 (inteiros)       ████████████████  16 colunas  (31%)
dbTimeStamp (datas) ████████          8 colunas   (16%)
wstr (texto)        ██████████████████████  19 colunas (37%)
numeric (decimal)   ████████          8 colunas   (16%)
```

## Totais por Tipo

| Tipo SSIS   | Quantidade | Percentual |
|-------------|------------|------------|
| i4          | 16         | 31%        |
| wstr        | 19         | 37%        |
| dbTimeStamp | 8          | 16%        |
| numeric     | 8          | 16%        |
| **Total**   | **51**     | **100%**   |

## Distribuição por Grupo Semântico

| Grupo                | Colunas |
|----------------------|---------|
| Ficha de Viagem      | 12      |
| Romaneio/Manifesto   | 11      |
| CTRB                 | 6       |
| CTe / Valores        | 8       |
| Geografico           | 4       |
| Partes               | 8       |
| CTe (identif.)       | 2       |
