# ETL_Sascar — Transformações

## Resumo

Este package **não possui componentes de transformação** no Data Flow (sem Derived Column, Lookup, Data Conversion, etc.). No entanto, a lógica de negócio está embutida nos **Script Components C#**.

---

## Fluxo 1: DFT - 1 Obter Lista de Veiculos

```
[SRC - API Lista Veiculos]          [Recordset Destination]
Script C# chamada SOAP         -->  User::VehicleList (Object)
obterVeiculos (qty=3000)             (ADO Recordset in-memory)
```

**Lógica do Script (main.cs):**
1. Define `SecurityProtocol = TLS 1.2`
2. Monta envelope SOAP `obterVeiculos` com usuário, senha, quantidade=3000, idVeiculo=0
3. Faz HTTP POST via `HttpWebRequest`
4. Parseia XML de resposta com `XmlDocument` e `XmlNamespaceManager`
5. Itera sobre nós `//ns0:obterVeiculosResponse/return`
6. Para cada nó: `SaidaVeiculosBuffer.AddRow()`, seta `idVeiculo` e `placa`

**Tratamento de Erros:**
- `WebException` é capturada
- Lê o stream de resposta da exceção se existir
- Dispara `ComponentMetaData.FireError()` — **aborta o componente**

---

## Fluxo 2: DFT - 2 Busca e Carga Posição (dentro do Foreach)

```
[SRC - API Posição Individual]      [ADO NET Destination]
Script C# chamada SOAP         -->  DW_Frota_UltimaPosicao1
usando CurrentPlaca e               10.100.86.89 / DWGrupolc
CurrentVehicleID do loop
```

**Variáveis lidas pelo script:**
- `User::CurrentVehicleID` (Int32) — índice 0 do Foreach
- `User::CurrentPlaca` (String) — índice 1 do Foreach

**Lógica do Script:** Chama endpoint de posição individual da Sascar para o veículo atual do loop, retorna 10 campos de posição.

---

## Pré-processamento (antes dos Data Flows)

**Task: SQLTask - 1 Truncate Base no DW**
```sql
TRUNCATE TABLE DW_Frota_UltimaPosicao1
```

---

## Task de Log (Script Task)

**SCR - Log de Progresso** executa após cada iteração do Foreach:

```csharp
int vehicleId = (int)Dts.Variables["User::CurrentVehicleID"].Value;
string placa = Dts.Variables["User::CurrentPlaca"].Value.ToString();
string progressMessage = string.Format("Progresso: Processando Veículo ID {0}, Placa: {1}", vehicleId, placa);
bool fireAgain = true;
Dts.Events.FireInformation(0, "Status do Loop", progressMessage, "", 0, ref fireAgain);
Dts.TaskResult = (int)DTSExecResult.Success;
```

> **Observação de typo:** O namespace da classe `ScriptMain` no arquivo `.cs` está como `ST_4bd4d3a8dbd81487e955a2121201cdf7c` (diferente do nome do projeto `ST_4bd4d43a8db1487e855a2121201cd75c`). Isso é uma inconsistência nos bytes do GUID no namespace, mas não impede compilação pois o SSIS usa o assembly compilado.

---

## Conversão Implícita de Tipos

A tabela destino armazena campos como VARCHAR/NVARCHAR(50) enquanto a origem entrega tipos numéricos. A conversão é feita implicitamente pelo ADO.NET provider. Não há Data Conversion explícita no pipeline.

| Campo         | Tipo Origem | Tipo Destino | Conversão Implícita    |
|---------------|-------------|--------------|------------------------|
| odometro      | i4          | wstr(50)     | int.ToString()         |
| ignicao       | i4          | wstr(50)     | int.ToString()         |
| velocidade    | i4          | wstr(50)     | int.ToString()         |
| dataPacote    | dbTimeStamp | wstr(50)     | datetime.ToString()    |
| longitude     | r8          | wstr(50)     | double.ToString()      |
| latitude      | r8          | wstr(50)     | double.ToString()      |
| idMotorista   | i4          | wstr(50)     | int.ToString()         |
| idVeiculo     | i4          | wstr(50)     | int.ToString()         |
