# ETL_Painel_CTE — Origem dos Dados

## Servidor de Origem

| Item            | Valor                                             |
|-----------------|---------------------------------------------------|
| Servidor        | 169.57.181.231                                    |
| Banco           | SOFTRAN_TRANSLUTE                                 |
| Usuário         | softran                                           |
| Connection Name | 169.57.181.231.SOFTRAN_TRANSLUTE.datalc           |
| Tipo            | ADO.NET (System.Data.SqlClient)                   |
| CommandTimeout  | 600 segundos                                      |

## Source: ADO NET Source (Data Flow "Load fCTE")

- **Componente:** ADO NET Source
- **Modo:** SqlCommand (query inline)
- **Referência de tabela base:** `dbo.gtcconhe` (alias `a`) — Conhecimentos de Transporte

### Query SQL Completa

```sql
SELECT
    CONVERT(VARCHAR(50), CONCAT(a.CdEmpresa, a.NrSeqControle, a.NrDoctoFiscal)) AS cdempresa,
    SUBSTRING(g.DsApelido, 1, 3) AS DsApelidoEmitente,
    a.NrCepColeta AS CEPColeta,
    a.NrCepCalcAte AS CEPDestino,
    a.dsplacaveiculo,
    a.NrPlacaReboque1,
    q.dsnome AS DsMotorista,
    CONVERT(MONEY, a.VlFretePeso) AS VlFretePeso,
    CONVERT(MONEY, a.VlFreteValor) AS VlFreteValor,
    CONVERT(MONEY, a.VlPedagio) AS VlPedagio,
    CONVERT(MONEY, a.VlGRIS) AS VlGRIS,
    CONVERT(MONEY, a.VlTotalPrestacao) AS VlTotalPrestacao,
    a.nrdoctofiscal,
    a.CdTpDoctoFiscal,
    f.dsapelido,
    CONVERT(VARCHAR, a.DtEmissao, 103) AS DtEmissao,
    CASE WHEN ISNULL(a.inConhecimento, 0) = 0 THEN 'Normal' ELSE 'Cancelado' END AS DsSituacao,
    CASE WHEN ISNULL(a.inTipoFrete, 0) = 0 THEN 'F' ELSE 'C' END AS DsTpFrete,
    CASE WHEN ISNULL(a.intipoemissao, 0) = 0  THEN 'Normal'
         WHEN ISNULL(a.intipoemissao, 0) = 1  THEN 'Dev Total'
         WHEN ISNULL(a.intipoemissao, 0) = 2  THEN 'Reentrega'
         WHEN ISNULL(a.intipoemissao, 0) = 3  THEN 'Dev Parcial'
         WHEN ISNULL(a.intipoemissao, 0) = 4  THEN 'Complementar'
         WHEN ISNULL(a.intipoemissao, 0) = 5  THEN 'Cotação de Frete'
         WHEN ISNULL(a.intipoemissao, 0) = 6  THEN 'Armazenagem'
         WHEN ISNULL(a.intipoemissao, 0) = 7  THEN 'Paletizacao'
         WHEN ISNULL(a.intipoemissao, 0) = 8  THEN 'Diaria'
         WHEN ISNULL(a.intipoemissao, 0) = 9  THEN 'Redespacho Prop'
         WHEN ISNULL(a.intipoemissao, 0) = 10 THEN 'Agendamento'
         WHEN ISNULL(a.intipoemissao, 0) = 11 THEN 'Redepacho'
         WHEN ISNULL(a.intipoemissao, 0) = 12 THEN 'SubContrato'
         WHEN ISNULL(a.intipoemissao, 0) = 13 THEN 'Refaturamento'
         WHEN ISNULL(a.intipoemissao, 0) = 14 THEN 'Substituição'
         WHEN ISNULL(a.intipoemissao, 0) = 15 THEN 'Anulação'
    END AS DsTipoEmissao,
    i.dsUF AS DsUFOrigem,
    j.DsUF AS DsUFDestino,
    CONVERT(VARCHAR, a.dtentrega, 103) AS dtentrega,
    CONVERT(VARCHAR, l.dtemissao, 103) AS DtEmissaoFatura,
    CONVERT(VARCHAR, m.dtvencimento, 103) AS dtvencimento,
    CONVERT(MONEY, a.QtPeso) AS QtPeso,
    CONVERT(MONEY, a.QtMetrosCubicos) AS QtMetrosCubicos,
    CONVERT(MONEY, a.VlMercadoria) AS VlMercadoria,
    CONVERT(MONEY, a.VlLiquido) AS VlLiquido,
    CONVERT(MONEY, a.VlICMS) AS VlICMS,
    CONVERT(MONEY, a.QtVolume) AS QtVolume,
    a.CdRemetente,
    b.dsentidade AS DsRemetente,
    a.CdDestinatario,
    c.dsentidade AS dsDestinatario,
    a.CdConsignatario,
    d.dsentidade AS dsConsignatario,
    a.CdInscricao,
    e.dsentidade AS dsPagador,
    a.CdEmpresaDestino,
    h.DsApelido AS DsEmpDest,
    i.dslocal AS CidadeColeta,
    j.dslocal AS CidadeEntrega,
    l.cdfatura,
    CASE WHEN ISNULL(c.InLocaldificilent, 0) = 0 THEN 'Nao' ELSE 'Sim' END AS InTDE,
    docto.NrConheOpLog AS DoctoOrigem,
    notas.NrNotaFiscalConcatenada AS NrNotaFiscal,
    situacao_arq.DsSituacaoArq,
    n.VlLiquido AS VlFreteOrigem,
    n.VlICMS AS VlICMSOrigem,
    o.dsnatureza,
    p.dstransporte,
    ocorrencia.DsUltimaOcorrencia,
    ocorrencia.DtUltimaOcorrencia,
    ocorrencia.DsUsuario,
    romaneio.NomeMotorista,
    romaneio.NrRomaneio,
    romaneio.NrPlacaRomaneio,
    manif.PlacaManifInterior,
    manif.DtSaidaParceiro,
    manif.DsItinerario,
    conemb.ArquivoConemb,
    conemb.DtEnvioConemb,
    ocoren.ArquivoOcoren,
    ocoren.DtEnvioOcoren,
    doccob.ArquivoDocCob,
    doccob.DtEnvioDocCob,
    pedido.Pedido,
    conteudo.ConteudoNF AS Conteudo,
    CONVERT(VARCHAR, previsao.DtPrevisaoCalculada, 103) AS [Previsão de Entrega],
    CONVERT(VARCHAR, r.DtDigitacao, 103) + ' ' + CONVERT(VARCHAR, r.DtDigitacao, 108) AS [Dt Inclusao Ocorrência],
    CONVERT(VARCHAR, r.DtMovimento, 103) + ' ' + CONVERT(VARCHAR, r.HrMovimento, 108) AS [Dt Ocorrencia],
    r.DsHistoricoEntrega AS [Descrição Ult Ocorrencia],
    r.DsUsuario AS [Usuário Ocorrencia],
    a.dsusuario AS [UsuarioEmissao]
FROM gtcconhe AS a WITH (NOLOCK)
LEFT JOIN siscli   AS b   WITH (NOLOCK) ON b.cdinscricao = a.cdremetente
LEFT JOIN siscli   AS c   WITH (NOLOCK) ON c.cdinscricao = a.cddestinatario
LEFT JOIN siscli   AS d   WITH (NOLOCK) ON d.cdinscricao = a.cdconsignatario
LEFT JOIN siscli   AS e   WITH (NOLOCK) ON e.cdinscricao = a.cdinscricao
LEFT JOIN sisclifa AS ee  WITH (NOLOCK) ON ee.cdinscricao = e.cdinscricao
LEFT JOIN sistdf   AS f   WITH (NOLOCK) ON f.cdtpdoctofiscal = a.cdtpdoctofiscal
LEFT JOIN sisempre AS g   WITH (NOLOCK) ON g.cdempresa = a.cdempresa
LEFT JOIN sisempre AS h   WITH (NOLOCK) ON h.cdempresa = a.cdempresadestino
LEFT JOIN siscep   AS i   WITH (NOLOCK) ON i.nrcep = a.nrcepcoleta
LEFT JOIN siscep   AS j   WITH (NOLOCK) ON j.nrcep = a.nrcepentrega
LEFT JOIN gtcfatit AS k   WITH (NOLOCK) ON k.cdempresaconhec = a.cdempresa AND k.nrseqcontrole = a.nrseqcontrole AND ISNULL(k.insituacao, 0) = 0
LEFT JOIN gtcfat   AS l   WITH (NOLOCK) ON l.cdempresa = k.cdempresa AND l.cdfatura = k.cdfatura AND l.cdparcela = k.cdparcela
LEFT JOIN gfatitu  AS m   WITH (NOLOCK) ON m.inpagarreceber = 1 AND m.cdfilial = l.cdempresa AND m.nrfatura = l.cdfatura AND m.cdparcela = l.cdparcela
LEFT JOIN gtcconhe AS n   WITH (NOLOCK) ON n.cdempresa = a.cdempresaref AND n.nrseqcontrole = a.nrseqcontroleREF
LEFT JOIN gtcnatur AS o   WITH (NOLOCK) ON o.cdnatureza = a.cdnatureza
LEFT JOIN gtctrans AS p   WITH (NOLOCK) ON p.cdtransporte = a.cdtransporte
LEFT JOIN gtcfundp AS q   WITH (NOLOCK) ON q.nrcpf = a.cdmotorista
LEFT JOIN dbo.ResultadosPrevisaoEntrega AS previsao WITH (NOLOCK) ON a.CdEmpresa = previsao.CdEmpresa AND a.NrSeqControle = previsao.NrSeqControle
OUTER APPLY (
    SELECT TOP 1 tb1.DsUsuario, tb1.DtDigitacao, tb1.DtMovimento, tb1.HrMovimento, tb2.DsHistoricoEntrega
    FROM GTCMOVEN AS tb1 WITH (NOLOCK)
    INNER JOIN GTCHisEn AS tb2 WITH (NOLOCK) ON tb1.CdOcorrencia = tb2.CdHistoricoEntrega
    WHERE a.NrSeqControle = tb1.NrSeqControle AND a.CdEmpresa = tb1.CdEmpresa
    ORDER BY tb1.DtDigitacao DESC
) AS r
OUTER APPLY (
    SELECT STUFF((
        SELECT '/' + LTRIM(RTRIM(CAST(nf.NrNotaFiscal AS VARCHAR(100))))
        FROM dbo.GTCNfCon AS nf WITH (NOLOCK)
        WHERE nf.CdEmpresa = a.CdEmpresa AND nf.NrSeqControle = a.NrSeqControle
        ORDER BY nf.NrNotaFiscal
        FOR XML PATH('')
    ), 1, 1, '') AS NrNotaFiscalConcatenada
) AS notas
OUTER APPLY (
    SELECT TOP 1 yy.NrConheOpLog
    FROM gtcnfcon AS xx
    LEFT JOIN gtcnf AS yy ON yy.cdremetente = xx.cdinscricao AND yy.nrserie = xx.nrserie AND yy.NrNotaFiscal = xx.NrNotaFiscal
    WHERE xx.cdempresa = a.cdempresa AND xx.nrseqcontrole = a.nrseqcontrole
) AS docto
OUTER APPLY (
    SELECT DsSituacaoArq = CASE
        WHEN NOT EXISTS (SELECT 1 FROM ccecceit AS situacao WHERE situacao.cdempresaconhec = a.cdempresa AND situacao.nrseqcontrole = a.nrseqcontrole) THEN 'Sem Protocolo'
        WHEN EXISTS (SELECT 1 FROM ccecceit AS situacao WHERE situacao.cdempresaconhec = a.cdempresa AND situacao.nrseqcontrole = a.nrseqcontrole AND ISNULL(situacao.insituacao, 99) = 1) THEN 'Arquivado'
        WHEN EXISTS (SELECT 1 FROM ccecceit AS situacao WHERE situacao.cdempresaconhec = a.cdempresa AND situacao.nrseqcontrole = a.nrseqcontrole AND ISNULL(situacao.insituacao, 99) = 0) THEN 'Em Transito'
    END
) AS situacao_arq
OUTER APPLY (
    SELECT TOP 1
        DsUltimaOcorrencia = yy.dshistoricoentrega,
        DtUltimaOcorrencia = CONVERT(VARCHAR, xx.Dtmovimento, 103),
        DsUsuario = xx.DsUsuario
    FROM gtcmoven AS xx
    LEFT JOIN gtchisen AS yy ON yy.cdhistoricoentrega = xx.cdocorrencia
    WHERE xx.cdempresa = a.cdempresa AND xx.nrseqcontrole = a.nrseqcontrole
    ORDER BY xx.cdsequencia DESC
) AS ocorrencia
OUTER APPLY (
    SELECT TOP 1
        NomeMotorista = mm.DsNome,
        NrRomaneio = CAST(xx.CdEmpresa AS VARCHAR(3)) + '-' + CAST(xx.CdRota AS VARCHAR(5)) + '-' + CAST(xx.CdRomaneio AS VARCHAR(6)),
        NrPlacaRomaneio = xx.nrplaca
    FROM cceroman AS xx
    LEFT JOIN cceromit AS yy ON yy.cdempresa = xx.cdempresa AND yy.cdrota = xx.cdrota AND yy.cdromaneio = xx.cdromaneio AND ISNULL(yy.insituacao, 0) <> 9
    LEFT JOIN gtcfundp AS mm ON xx.NrCPFMotorista = mm.NrCPF
    WHERE ISNULL(xx.insituacao, 0) = 0 AND yy.cdempresacoletaentrega = a.cdempresa AND yy.nrseqcontrole = a.nrseqcontrole
    ORDER BY xx.dtromaneio DESC
) AS romaneio
OUTER APPLY (
    SELECT TOP 1
        PlacaManifInterior = yy.nrplaca,
        DtSaidaParceiro = yy.dtsaida,
        DsItinerario = SUBSTRING(yy.nrmanifesto, 1, 7)
    FROM gtcmancn AS xx
    LEFT JOIN gtcman AS yy ON yy.nrmanifesto = xx.nrmanifesto
    LEFT JOIN gtcitine AS zz ON zz.cditinerario = SUBSTRING(yy.nrmanifesto, 1, 7)
    LEFT JOIN sisempre AS zzz ON zzz.cdempresa = zz.cdempresadestino
    WHERE ISNULL(xx.insituacao, 0) = 0 AND ISNULL(yy.insituacao, 0) = 0
      AND xx.cdempresa = a.cdempresa AND xx.nrseqcontrole = a.nrseqcontrole
      AND zzz.cdempresa >= 500 AND zzz.cdempresa <= 999
) AS manif
OUTER APPLY (
    SELECT TOP 1 DsNomeArquivo AS ArquivoConemb, DtEnvio AS DtEnvioConemb
    FROM EDIENVLG WHERE ISNULL(CdTpLayout, 0) = 0 AND CdEmpresa = a.CdEmpresa AND NrSeqControle = a.NrSeqControle
    ORDER BY DtEnvio DESC
) AS conemb
OUTER APPLY (
    SELECT TOP 1 DsNomeArquivo AS ArquivoOcoren, DtEnvio AS DtEnvioOcoren
    FROM EDIENVLG WHERE ISNULL(CdTpLayout, 0) = 1 AND CdEmpresa = a.CdEmpresa AND NrSeqControle = a.NrSeqControle
    ORDER BY DtEnvio DESC
) AS ocoren
OUTER APPLY (
    SELECT TOP 1 DsNomeArquivo AS ArquivoDocCob, DtEnvio AS DtEnvioDocCob
    FROM EDIENVLG WHERE ISNULL(CdTpLayout, 0) = 2 AND CdEmpresaFat = l.CdEmpresa AND CdFatura = l.CdFatura
    ORDER BY DtEnvio DESC
) AS doccob
OUTER APPLY (
    SELECT TOP 1 NrPedidoColeta AS Pedido
    FROM CCECOLET WHERE CdEmpresa = a.CdEmpresaColeta AND NrColeta = a.NrColeta
) AS pedido
OUTER APPLY (
    SELECT TOP 1 (NF.NrCFOP + '-' + ISNULL(NN.DsNaturezaOperacao, '-')) AS ConteudoNF
    FROM GTCNFCON AS YY
    LEFT JOIN GTCNF AS NF ON NF.CdRemetente = YY.CdInscricao AND NF.NrSerie = YY.NrSerie AND YY.NrNotaFiscal = NF.NrNotaFiscal
    LEFT JOIN SISNATOP AS NN ON NN.NrCFOP = NF.NrCFOP
    WHERE YY.CdEmpresa = a.CdEmpresa AND YY.NrSeqControle = a.NrSeqControle AND ISNULL(NF.NrCFOP, '') <> ''
) AS conteudo
WHERE a.DtCancelamento IS NULL
  AND a.cdremetente IS NOT NULL
  AND a.dtemissao >= '20240101';
```

### Tabela de Colunas da Origem (ADO NET Source Output)

| # | Nome da Coluna              | Tipo SSIS    | Comprimento / Precisão |
|---|-----------------------------|--------------|------------------------|
| 1 | cdempresa                   | wstr         | 50                     |
| 2 | DsApelidoEmitente           | wstr         | 3                      |
| 3 | CEPColeta                   | i4           | —                      |
| 4 | CEPDestino                  | i4           | —                      |
| 5 | dsplacaveiculo              | wstr         | 8                      |
| 6 | NrPlacaReboque1             | wstr         | 8                      |
| 7 | DsMotorista                 | wstr         | 40                     |
| 8 | VlFretePeso                 | cy (money)   | —                      |
| 9 | VlFreteValor                | cy (money)   | —                      |
| 10 | VlPedagio                  | cy (money)   | —                      |
| 11 | VlGRIS                     | cy (money)   | —                      |
| 12 | VlTotalPrestacao           | cy (money)   | —                      |
| 13 | nrdoctofiscal              | i4           | —                      |
| 14 | CdTpDoctoFiscal            | i4           | —                      |
| 15 | dsapelido                  | wstr         | 10                     |
| 16 | DtEmissao                  | wstr         | 30                     |
| 17 | DsSituacao                 | wstr         | 9                      |
| 18 | DsTpFrete                  | wstr         | 1                      |
| 19 | DsTipoEmissao              | wstr         | 16                     |
| 20 | DsUFOrigem                 | wstr         | 2                      |
| 21 | DsUFDestino                | wstr         | 2                      |
| 22 | dtentrega                  | wstr         | 30                     |
| 23 | DtEmissaoFatura            | wstr         | 30                     |
| 24 | dtvencimento               | wstr         | 30                     |
| 25 | QtPeso                     | cy (money)   | —                      |
| 26 | QtMetrosCubicos            | cy (money)   | —                      |
| 27 | VlMercadoria               | cy (money)   | —                      |
| 28 | VlLiquido                  | cy (money)   | —                      |
| 29 | VlICMS                     | cy (money)   | —                      |
| 30 | QtVolume                   | cy (money)   | —                      |
| 31 | CdRemetente                | wstr         | 14                     |
| 32 | DsRemetente                | wstr         | 100                    |
| 33 | CdDestinatario             | wstr         | 14                     |
| 34 | dsDestinatario             | wstr         | 100                    |
| 35 | CdConsignatario            | wstr         | 14                     |
| 36 | dsConsignatario            | wstr         | 100                    |
| 37 | CdInscricao                | wstr         | 14                     |
| 38 | dsPagador                  | wstr         | 100                    |
| 39 | CdEmpresaDestino           | i4           | —                      |
| 40 | DsEmpDest                  | wstr         | 10                     |
| 41 | CidadeColeta               | wstr         | 30                     |
| 42 | CidadeEntrega              | wstr         | 30                     |
| 43 | cdfatura                   | i4           | —                      |
| 44 | InTDE                      | wstr         | 3                      |
| 45 | DoctoOrigem                | i4           | —                      |
| 46 | NrNotaFiscal               | nText        | 2147483647 (MAX)       |
| 47 | DsSituacaoArq              | wstr         | 13                     |
| 48 | VlFreteOrigem              | numeric      | p=14, s=4              |
| 49 | VlICMSOrigem               | numeric      | p=14, s=4              |
| 50 | dsnatureza                 | wstr         | 40                     |
| 51 | dstransporte               | wstr         | 40                     |
| 52 | DsUltimaOcorrencia         | wstr         | 50                     |
| 53 | DtUltimaOcorrencia         | wstr         | 30                     |
| 54 | DsUsuario                  | wstr         | 10                     |
| 55 | NomeMotorista              | wstr         | 40                     |
| 56 | NrRomaneio                 | wstr         | 16                     |
| 57 | NrPlacaRomaneio            | wstr         | 8                      |
| 58 | PlacaManifInterior         | wstr         | 8                      |
| 59 | DtSaidaParceiro            | dbTimeStamp  | —                      |
| 60 | DsItinerario               | wstr         | 7                      |
| 61 | ArquivoConemb              | wstr         | 255                    |
| 62 | DtEnvioConemb              | dbTimeStamp  | —                      |
| 63 | ArquivoOcoren              | wstr         | 255                    |
| 64 | DtEnvioOcoren              | dbTimeStamp  | —                      |
| 65 | ArquivoDocCob              | wstr         | 255                    |
| 66 | DtEnvioDocCob              | dbTimeStamp  | —                      |
| 67 | Pedido                     | wstr         | 30                     |
| 68 | Conteudo                   | wstr         | 111                    |
| 69 | Previsão de Entrega        | wstr         | 30                     |
| 70 | Dt Inclusao Ocorrência     | wstr         | 61                     |
| 71 | Dt Ocorrencia              | wstr         | 61                     |
| 72 | Descrição Ult Ocorrencia   | wstr         | 50                     |
| 73 | Usuário Ocorrencia         | wstr         | 10                     |
| 74 | UsuarioEmissao             | wstr         | 10                     |

**Total: 74 colunas**

### Tabelas de Origem Envolvidas

| Alias | Tabela               | Relação              |
|-------|----------------------|----------------------|
| a     | gtcconhe             | Principal — CT-e     |
| b     | siscli               | Remetente            |
| c     | siscli               | Destinatário         |
| d     | siscli               | Consignatário        |
| e     | siscli               | Pagador (cdinscricao)|
| ee    | sisclifa             | Fatura cliente       |
| f     | sistdf               | Tipo Documento Fiscal|
| g     | sisempre             | Empresa emitente     |
| h     | sisempre             | Empresa destino      |
| i     | siscep               | CEP coleta           |
| j     | siscep               | CEP entrega          |
| k     | gtcfatit             | Fatura itens         |
| l     | gtcfat               | Fatura               |
| m     | gfatitu              | Vencimento financeiro|
| n     | gtcconhe             | CT-e de referência   |
| o     | gtcnatur             | Natureza             |
| p     | gtctrans             | Transporte           |
| q     | gtcfundp             | Motorista (CPF)      |
| previsao | ResultadosPrevisaoEntrega | Previsão de entrega (tabela pré-calculada) |
| r     | GTCMOVEN + GTCHisEn  | Última movimentação  |
| notas | GTCNfCon             | Notas fiscais concatenadas |
| docto | gtcnfcon + gtcnf     | Documento de origem  |
| situacao_arq | ccecceit    | Situação arquivo EDI |
| ocorrencia | gtcmoven + gtchisen | Ocorrência resumida |
| romaneio | cceroman + cceromit + gtcfundp | Romaneio |
| manif | gtcmancn + gtcman + gtcitine + sisempre | Manifesto interior |
| conemb | EDIENVLG (layout 0) | Arquivo CONEMB       |
| ocoren | EDIENVLG (layout 1) | Arquivo OCOREN       |
| doccob | EDIENVLG (layout 2) | Arquivo DocCob       |
| pedido | CCECOLET            | Pedido de coleta     |
| conteudo | GTCNFCON + GTCNF + SISNATOP | Conteúdo NF |

### Filtros da Query

```sql
WHERE a.DtCancelamento IS NULL
  AND a.cdremetente IS NOT NULL
  AND a.dtemissao >= '20240101'
```
