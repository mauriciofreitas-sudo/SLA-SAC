# ETL_Sascar — Destino

## Servidor de Destino

| Propriedade         | Valor                          |
|---------------------|--------------------------------|
| Servidor            | 10.100.86.89                   |
| Banco de Dados      | DWGrupolc                      |
| Usuário             | sqldba                         |
| Tipo de Conexão     | ADO.NET (SqlClient)            |
| TrustServerCertificate | True                        |

---

## Destination Component: ADO NET Destination (DFT-2)

| Propriedade               | Valor                        |
|---------------------------|------------------------------|
| Nome do Componente        | ADO NET Destination          |
| Tabela Destino            | `DW_Frota_UltimaPosicao1`    |
| BatchSize                 | 0 (usa buffer interno SSIS)  |
| CommandTimeout            | 30 segundos                  |
| UseBulkInsertWhenPossible | true                         |

---

## Estratégia de Carga

**TRUNCATE + INSERT por iteração (Full Reload com loop veículo a veículo)**

1. **Task 1:** `TRUNCATE TABLE DW_Frota_UltimaPosicao1` — apaga todos os dados
2. **DFT-1:** Carrega lista de veículos (até 3.000) em variável Object em memória
3. **Foreach Loop:** Para cada veículo, chama a API e insere 1 linha na tabela

> Esta abordagem significa que a tabela é populada linha a linha em um loop. Com 300+ veículos, são 300+ chamadas HTTP sequenciais.

---

## Schema da Tabela Destino `DW_Frota_UltimaPosicao1`

| # | Coluna        | Tipo SSIS   | Tamanho (Ext) | Tipo no DW (inferido)    | Nulável |
|---|---------------|-------------|----------------|--------------------------|---------|
| 1 | odometro      | i4          | wstr, 50 (ext) | Mismatch: source=int, dest=wstr(50) | Sim |
| 2 | ignicao       | i4          | wstr, 50 (ext) | Mismatch: source=int, dest=wstr(50) | Sim |
| 3 | velocidade    | i4          | wstr, 50 (ext) | Mismatch: source=int, dest=wstr(50) | Sim |
| 4 | dataPacote    | dbTimeStamp | wstr, 50 (ext) | Mismatch: source=datetime, dest=wstr(50) | Sim |
| 5 | longitude     | r8          | wstr, 50 (ext) | Mismatch: source=float, dest=wstr(50) | Sim |
| 6 | latitude      | r8          | wstr, 50 (ext) | Mismatch: source=float, dest=wstr(50) | Sim |
| 7 | nomeMotorista | wstr, 255   | wstr, 255      | nvarchar(255)            | Sim     |
| 8 | idMotorista   | i4          | wstr, 50 (ext) | Mismatch: source=int, dest=wstr(50) | Sim |
| 9 | idVeiculo     | i4          | wstr, 50 (ext) | Mismatch: source=int, dest=wstr(50) | Sim |
| 10 | placa        | wstr, 20    | wstr, 20       | nvarchar(20)             | Sim     |

> **ALERTA CRÍTICO:** O metadata externo (schema da tabela no DW) registra 7 das 10 colunas como `wstr/str` com tamanho 50, enquanto a origem entrega `i4` (int) ou `r8` (float). Isso indica que a tabela `DW_Frota_UltimaPosicao1` armazena esses campos como VARCHAR/NVARCHAR, perdendo a natureza numérica dos dados (odometro, ignicao, velocidade, latitude, longitude, idMotorista, idVeiculo). O ADO.NET usa `AllowImplicitStringConversion` implicitamente via conversão do provider.

---

## Tabela de Mismatches de Tipo

| Coluna        | Tipo SSIS (origem) | Tipo Externo (tabela DW) | Problema                         |
|---------------|--------------------|--------------------------|----------------------------------|
| odometro      | i4                 | wstr(50)                 | Número salvo como texto          |
| ignicao       | i4                 | wstr(50)                 | Flag 0/1 salvo como texto        |
| velocidade    | i4                 | wstr(50)                 | Número salvo como texto          |
| dataPacote    | dbTimeStamp        | wstr(50)                 | Data salva como texto            |
| longitude     | r8                 | wstr(50)                 | Float salvo como texto           |
| latitude      | r8                 | wstr(50)                 | Float salvo como texto           |
| idMotorista   | i4                 | wstr(50)                 | ID numérico salvo como texto     |
| idVeiculo     | i4                 | wstr(50)                 | ID numérico salvo como texto     |
