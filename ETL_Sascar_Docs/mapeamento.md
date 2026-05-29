# ETL_Sascar — Mapeamento de Colunas

## Visão Geral

| Propriedade               | Fluxo 1 (DFT-1)         | Fluxo 2 (DFT-2)                  |
|---------------------------|--------------------------|----------------------------------|
| Origem                    | API SOAP Sascar          | API SOAP Sascar (por veículo)    |
| Destino                   | User::VehicleList (RAM)  | DW_Frota_UltimaPosicao1          |
| Total de colunas          | 2                        | 10                               |

---

## Mapeamento DFT-1: Lista de Veículos

| # | Coluna Origem (API) | Tipo SSIS | Tamanho | Coluna Destino (Recordset) | Grupo     |
|---|---------------------|-----------|---------|---------------------------|-----------|
| 1 | idVeiculo           | i4        | —       | idVeiculo                 | Veículo   |
| 2 | placa               | wstr      | 20      | placa                     | Veículo   |

---

## Mapeamento DFT-2: Posição Individual (dentro do Foreach)

| # | Coluna Origem (API)  | Tipo SSIS   | Tamanho | Coluna Destino (DW)   | Tipo Externo | Grupo       |
|---|----------------------|-------------|---------|-----------------------|--------------|-------------|
| 1 | odometro             | i4          | —       | odometro              | wstr(50)     | Telemetria  |
| 2 | ignicao              | i4          | —       | ignicao               | wstr(50)     | Telemetria  |
| 3 | velocidade           | i4          | —       | velocidade            | wstr(50)     | Telemetria  |
| 4 | dataPacote           | dbTimeStamp | —       | dataPacote            | wstr(50)     | Temporal    |
| 5 | longitude            | r8          | —       | longitude             | wstr(50)     | GPS         |
| 6 | latitude             | r8          | —       | latitude              | wstr(50)     | GPS         |
| 7 | nomeMotorista        | wstr        | 255     | nomeMotorista         | wstr(255)    | Motorista   |
| 8 | idMotorista          | i4          | —       | idMotorista           | wstr(50)     | Motorista   |
| 9 | idVeiculo            | i4          | —       | idVeiculo             | wstr(50)     | Veículo     |
| 10 | placa               | wstr        | 20      | placa                 | wstr(20)     | Veículo     |

---

## Distribuição por Tipo de Dado (DFT-2)

```
i4 (inteiros)       ████████████  4 colunas  (40%)
r8 (double/float)   ████          2 colunas  (20%)
wstr (texto)        ████████      2 colunas* (20%) (* + 2 de wstr: nomeMotorista, placa)
dbTimeStamp (data)  ██            1 coluna   (10%)
```

| Tipo SSIS   | Quantidade | Percentual |
|-------------|------------|------------|
| i4          | 4          | 40%        |
| r8          | 2          | 20%        |
| wstr        | 3          | 30%        |
| dbTimeStamp | 1          | 10%        |
| **Total**   | **10**     | **100%**   |

---

## Observação sobre Mismatch de Tipos

8 das 10 colunas da tabela destino armazenam dados numéricos/datas como VARCHAR(50). A conversão é implícita pelo ADO.NET. Isso impede ordenação correta por valor e uso de funções de data/número diretamente no DW.
