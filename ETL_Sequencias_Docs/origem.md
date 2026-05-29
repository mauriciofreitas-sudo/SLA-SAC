# ETL_Sequencias — Origem

## Servidor de Origem

| Propriedade            | Valor                         |
|------------------------|-------------------------------|
| Servidor               | 169.57.181.231                |
| Banco de Dados         | SOFTRAN_TRANSLUTE             |
| Usuário                | datalc                        |
| Tipo de Conexão        | ADO.NET (SqlClient)           |
| TrustServerCertificate | True                          |

## Source Component: ADO NET Source

| Propriedade         | Valor                                |
|---------------------|--------------------------------------|
| Nome                | ADO NET Source                       |
| TableOrViewName     | `"dbo"."vw_Entregas_Sequencia"`      |
| AccessMode          | 0 (Table or View — usa TableOrViewName) |
| SqlCommand          | Query complexa (ver abaixo)          |
| CommandTimeout      | 600 segundos                         |
| ValidateExternalMetadata | False                           |

> **Observação:** O campo `SqlCommand` contém a query completa (não usa AccessMode=2), mas `AccessMode` está como 0. Na prática o SSIS usa `TableOrViewName` como fallback quando AccessMode=0. A query detalhada abaixo pode ser a query **usada durante o design-time** para capturar metadata, mas a execução runtime pode usar a view `vw_Entregas_Sequencia`.

---

## Query SQL da Origem

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
LEFT JOIN siscli AS b WITH (NOLOCK) ON b.cdinscricao = a.cdremetente
LEFT JOIN siscli AS c WITH (NOLOCK) ON c.cdinscricao = a.cddestinatario
LEFT JOIN siscli AS d WITH (NOLOCK) ON d.cdinscricao = a.cdconsignatario
LEFT JOIN siscli AS e WITH (NOLOCK) ON e.cdinscricao = a.cdinscricao
LEFT JOIN sisclifa AS ee WITH (NOLOCK) ON ee.cdinscricao = e.cdinscricao
LEFT JOIN sistdf AS f WITH (NOLOCK) ON f.cdtpdoctofiscal = a.cdtpdoctofiscal
LEFT JOIN sisempre AS g WITH (NOLOCK) ON g.cdempresa = a.cdempresa
LEFT JOIN sisempre AS h WITH (NOLOCK) ON h.cdempresa = a.cdempresadestino
LEFT JOIN siscep AS i WITH (NOLOCK) ON i.nrcep = a.nrcepcoleta
LEFT JOIN siscep AS j WITH (NOLOCK) ON j.nrcep = a.nrcepentrega
LEFT JOIN gtcfatit AS k ON k.cdempresaconhec = a.cdempresa AND k.nrseqcontrole = a.nrseqcontrole AND ISNULL(k.insituacao, 0) = 0
LEFT JOIN gtcfat AS l ON l.cdempresa = k.cdempresa AND l.cdfatura = k.cdfatura AND l.cdparcela = k.cdparcela
LEFT JOIN gfatitu AS m ON m.inpagarreceber = 1 AND m.cdfilial = l.cdempresa AND m.nrfatura = l.cdfatura AND m.cdparcela = l.cdparcela
LEFT JOIN gtcconhe AS n ON n.cdempresa = a.cdempresaref AND n.nrseqcontrole = a.nrseqcontroleREF
LEFT JOIN gtcnatur AS o ON o.cdnatureza = a.cdnatureza
LEFT JOIN gtctrans AS p ON p.cdtransporte = a.cdtransporte
LEFT JOIN gtcfundp AS q ON q.nrcpf = a.cdmotorista
LEFT JOIN dbo.ResultadosPrevisaoEntrega AS previsao ON a.CdEmpresa = previsao.CdEmpresa AND a.NrSeqControle = previsao.NrSeqControle
OUTER APPLY (SELECT TOP 1 tb1.DsUsuario, tb1.DtDigitacao, tb1.DtMovimento, tb1.HrMovimento, tb2.DsHistoricoEntrega
    FROM GTCMOVEN AS tb1 WITH (NOLOCK)
    INNER JOIN GTCHisEn AS tb2 WITH (NOLOCK) ON tb1.CdOcorrencia = tb2.CdHistoricoEntrega
    WHERE a.NrSeqControle = tb1.NrSeqControle AND a.CdEmpresa = tb1.CdEmpresa
    ORDER BY tb1.DtDigitacao DESC) AS r
OUTER APPLY (SELECT STUFF((SELECT '/' + LTRIM(RTRIM(CAST(nf.NrNotaFiscal AS VARCHAR(100))))
    FROM dbo.GTCNfCon AS nf WITH (NOLOCK)
    WHERE nf.CdEmpresa = a.CdEmpresa AND nf.NrSeqControle = a.NrSeqControle
    ORDER BY nf.NrNotaFiscal FOR XML PATH('')), 1, 1, '') AS NrNotaFiscalConcatenada) AS notas
OUTER APPLY (SELECT TOP 1 yy.NrConheOpLog FROM gtcnfcon AS xx
    LEFT JOIN gtcnf AS yy ON yy.cdremetente = xx.cdinscricao AND yy.nrserie = xx.nrserie AND yy.NrNotaFiscal = xx.NrNotaFiscal
    WHERE xx.cdempresa = a.cdempresa AND xx.nrseqcontrole = a.nrseqcontrole) AS docto
OUTER APPLY (SELECT DsSituacaoArq = CASE
    WHEN NOT EXISTS (...ccecceit...) THEN 'Sem Protocolo'
    WHEN EXISTS (...insituacao=1...) THEN 'Arquivado'
    WHEN EXISTS (...insituacao=0...) THEN 'Em Transito' END) AS situacao_arq
OUTER APPLY (SELECT TOP 1 DsUltimaOcorrencia=yy.dshistoricoentrega, ... FROM gtcmoven xx
    LEFT JOIN gtchisen yy ON ... WHERE ... ORDER BY xx.cdsequencia DESC) AS ocorrencia
OUTER APPLY (SELECT TOP 1 NomeMotorista, NrRomaneio, NrPlacaRomaneio FROM cceroman xx ... ) AS romaneio
OUTER APPLY (SELECT TOP 1 PlacaManifInterior, DtSaidaParceiro, DsItinerario FROM gtcmancn xx ...) AS manif
OUTER APPLY (SELECT TOP 1 ArquivoConemb, DtEnvioConemb FROM EDIENVLG WHERE CdTpLayout=0 ...) AS conemb
OUTER APPLY (SELECT TOP 1 ArquivoOcoren, DtEnvioOcoren FROM EDIENVLG WHERE CdTpLayout=1 ...) AS ocoren
OUTER APPLY (SELECT TOP 1 ArquivoDocCob, DtEnvioDocCob FROM EDIENVLG WHERE CdTpLayout=2 ...) AS doccob
OUTER APPLY (SELECT TOP 1 NrPedidoColeta AS Pedido FROM CCECOLET WHERE ...) AS pedido
OUTER APPLY (SELECT TOP 1 (NF.NrCFOP + '-' + ISNULL(NN.DsNaturezaOperacao, '-')) AS ConteudoNF ...) AS conteudo
WHERE a.DtCancelamento IS NULL
  AND a.cdremetente IS NOT NULL
  AND a.dtemissao >= '20240101';
```

---

## Colunas da Saída Mapeadas para o Destino (30 colunas)

| # | Coluna                    | Tipo SSIS   | Tamanho     | Descrição Semântica                              |
|---|---------------------------|-------------|-------------|--------------------------------------------------|
| 1  | Empresa                  | wstr        | 23          | Nome/código da empresa emissora                  |
| 2  | NrPlaca                  | wstr        | 8           | Placa do veículo                                 |
| 3  | DtFechamento             | dbTimeStamp | —           | Data de fechamento do CTe                        |
| 4  | NrEvento                 | i8 (bigint) | —           | Número do evento                                 |
| 5  | CdEmpresa                | i4          | —           | Código numérico da empresa                       |
| 6  | NrFicha                  | i4          | —           | Número da ficha de viagem                        |
| 7  | DtEmissao                | dbTimeStamp | —           | Data de emissão do CTe                           |
| 8  | RomaneioManifesto        | wstr        | 20          | Número do romaneio/manifesto                     |
| 9  | CepDestinoManifesto      | i4          | —           | CEP de destino do manifesto                      |
| 10 | CidadeDestinoManifesto   | wstr        | 30          | Cidade de destino do manifesto                   |
| 11 | UFDestinoManifesto       | wstr        | 2           | UF de destino do manifesto                       |
| 12 | Tipo                     | wstr        | 9           | Tipo do CTe (ex: Normal, Dev Total)              |
| 13 | EmpresaCTE               | i4          | —           | Código da empresa do CTe                         |
| 14 | Cdremetente              | wstr        | 14          | CNPJ/CPF do remetente                            |
| 15 | Remetente                | wstr        | 100         | Nome do remetente                                |
| 16 | Destinatario             | wstr        | 14          | CNPJ/CPF do destinatário                         |
| 17 | DsEntidade               | wstr        | 100         | Nome do destinatário                             |
| 18 | NumeroCTE                | i4          | —           | Número do CT-e                                   |
| 19 | CepEntregaCTE            | i4          | —           | CEP de entrega do CTe                            |
| 20 | CidadeEntregaCTE         | wstr        | 30          | Cidade de entrega do CTe                         |
| 21 | UFEntregaCTE             | wstr        | 2           | UF de entrega do CTe                             |
| 22 | NrSeqControle            | i4          | —           | Número de sequência de controle do CTe           |
| 23 | CdTpDoctoFiscal          | i4          | —           | Código do tipo de documento fiscal               |
| 24 | DtPrevEntrega            | dbTimeStamp | —           | Data prevista de entrega                         |
| 25 | CTeDtEntrega             | dbTimeStamp | —           | Data real de entrega do CTe                      |
| 26 | DsUltimaOcorrencia       | wstr        | 30          | Descrição da última ocorrência de entrega        |
| 27 | CodigoOcorrencia         | wstr        | 30          | Código da última ocorrência                      |
| 28 | Origem_COLETA            | i4          | —           | CEP de origem da coleta                          |
| 29 | CidadeOrigemColeta       | wstr        | 30          | Cidade de origem da coleta                       |
| 30 | UFOrigemColeta           | wstr        | 2           | UF de origem da coleta                           |
| 31 | OrigemFicha              | r8 (float)  | —           | Código numérico de origem da ficha               |
| 32 | CidadeOrigemFicha        | wstr        | 30          | Cidade de origem da ficha                        |
| 33 | UFOrigemFicha            | wstr        | 2           | UF de origem da ficha                            |
| 34 | VlBaseCalculo            | numeric     | p14, s4     | Valor base de cálculo do frete                   |
| 35 | VlFretePeso              | numeric     | p14, s4     | Valor do frete por peso                          |
| 36 | Qtpeso                   | numeric     | p14, s4     | Quantidade de peso                               |
| 37 | DtAgendamentoEntregaCTe  | dbTimeStamp | —           | Data de agendamento de entrega do CTe            |
