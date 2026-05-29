# ETL_Sascar — Origem

## Fonte de Dados: API SOAP Sascar

Este package **não usa um banco de dados relacional** como origem. Os dados são obtidos diretamente via chamadas HTTP SOAP para a API de integração da Sascar.

| Propriedade     | Valor                                                      |
|-----------------|------------------------------------------------------------|
| Tipo de Fonte   | Web Service SOAP (HTTP)                                    |
| URL             | https://sasintegra.sascar.com.br/SasIntegra/SasIntegraWSService |
| WSDL            | https://sasintegra.sascar.com.br/SasIntegra/SasIntegraWSService?wsdl |
| Protocolo TLS   | TLS 1.2 (forçado via `SecurityProtocolType.Tls12`)        |
| Autenticação    | Usuário/Senha embarcados no script C#                      |
| Usuário API     | INTERNATRANSLUTE                                           |
| Senha API       | sascar                                                     |

---

## Source 1: SRC - API Lista Veiculos (DFT-1)

**Tipo:** Script Component (C#) — Componente de Origem personalizado

**Método SOAP chamado:** `obterVeiculos`

**Payload SOAP:**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="http://webservice.web.integracao.sascar.com.br/">
   <soapenv:Header/>
   <soapenv:Body>
      <web:obterVeiculos>
         <usuario>INTERNATRANSLUTE</usuario>
         <senha>sascar</senha>
         <quantidade>3000</quantidade>
         <idVeiculo>0</idVeiculo>
      </web:obterVeiculos>
   </soapenv:Body>
</soapenv:Envelope>
```

**Lógica:** Parseia o XML de resposta, extrai os nós `obterVeiculosResponse/return`, popula o buffer `SaidaVeiculos`.

**Colunas de Saída (SaidaVeiculos):**

| # | Coluna    | Tipo SSIS | Tamanho | Descrição                  |
|---|-----------|-----------|---------|----------------------------|
| 1 | idVeiculo | i4 (int)  | —       | ID único do veículo na Sascar |
| 2 | placa     | wstr      | 20      | Placa do veículo           |

**Destino dentro do DFT-1:** Recordset Destination → variável `User::VehicleList`

---

## Source 2: SRC - API Posição Individual (DFT-2, dentro do Foreach)

**Tipo:** Script Component (C#) — Componente de Origem personalizado

**Variáveis lidas:** `User::CurrentPlaca` (String), `User::CurrentVehicleID` (Int32)

**Método SOAP chamado:** (não explicitado no código fonte preservado no dtsx, mas chama endpoint de última posição por veículo usando as variáveis `CurrentPlaca` e `CurrentVehicleID` recebidas do Foreach Loop)

**Colunas de Saída (SaidaUltimaPosicao):**

| # | Coluna       | Tipo SSIS   | Tamanho | Descrição                              |
|---|--------------|-------------|---------|----------------------------------------|
| 1 | odometro     | i4 (int)    | —       | Odômetro do veículo em km              |
| 2 | ignicao      | i4 (int)    | —       | Estado da ignição (0=desligado, 1=ligado) |
| 3 | velocidade   | i4 (int)    | —       | Velocidade atual em km/h               |
| 4 | dataPacote   | dbTimeStamp | —       | Data/hora do pacote GPS                |
| 5 | longitude    | r8 (double) | —       | Longitude geográfica                   |
| 6 | latitude     | r8 (double) | —       | Latitude geográfica                    |
| 7 | nomeMotorista | wstr       | 255     | Nome do motorista identificado         |
| 8 | idMotorista  | i4 (int)    | —       | ID do motorista na Sascar              |
| 9 | idVeiculo    | i4 (int)    | —       | ID do veículo na Sascar                |
| 10 | placa        | wstr        | 20      | Placa do veículo                       |

---

## Observação sobre Conexões de Origem Não Utilizadas

O package define as seguintes conexões que **não participam** do fluxo de dados ativo:

- **CCM_JornadaMotoristaCache** (CACHE): Cache com colunas `descricaoEventoTempoDirecao` (wstr,255), `dataInicio` (datetime), `idMotorista` (i4). Provavelmente um artefato de desenvolvimento para lookup de motorista que foi abandonado.
- **Excel Connection Manager**: Aponta para `C:\Users\luiz.costa\Desktop\Output_Sascar1.xlsx` — path absoluto na máquina do desenvolvedor, inutilizável em produção.
- **HTTP Connection Manager**: Aponta para o WSDL da Sascar — definida mas os scripts usam `HttpWebRequest` direto, não esse connection manager.
