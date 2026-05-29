# ETL_Sascar — Issues e Observações

## Tabela Resumo de Severidades

| Severidade | Quantidade | Descrição Geral                              |
|------------|------------|----------------------------------------------|
| ALTA       | 4          | Problemas críticos de segurança e dados      |
| MEDIA      | 4          | Problemas de design e performance            |
| BAIXA      | 3          | Melhorias de manutenibilidade                |

---

## Issues Encontrados

### [ALTA-01] Credenciais da API embutidas em texto claro no script

**Localização:** Script `main.cs` do componente `SRC - API Lista Veiculos`

```csharp
<usuario>INTERNATRANSLUTE</usuario>
<senha>sascar</senha>
```

As credenciais de acesso à API Sascar estão em texto literal no código-fonte do script, que é armazenado em texto claro dentro do arquivo `.dtsx` (XML). Qualquer pessoa com acesso ao arquivo pode ler as credenciais.

**Sugestão:** Mover para variáveis SSIS protegidas (Encrypted) ou para parâmetros do projeto SSIS com EncryptSensitiveWithUserKey/Password.

---

### [ALTA-02] 8 colunas numéricas armazenadas como VARCHAR na tabela destino

**Localização:** Schema externo de `ADO NET Destination` (DFT-2)

Campos como `odometro`, `ignicao`, `velocidade`, `latitude`, `longitude`, `dataPacote`, `idMotorista` e `idVeiculo` têm tipo `wstr(50)` na tabela DW em vez de tipos nativos (int, float, datetime).

**Impacto:** Impossibilidade de cálculos matemáticos corretos (ex.: média de velocidade, distância percorrida), ordenação incorreta, joins lentos.

**Sugestão:** Alterar o schema da tabela `DW_Frota_UltimaPosicao1` para usar tipos corretos: `INT`, `FLOAT`, `DATETIME`.

---

### [ALTA-03] Performance: chamada de API por veículo em loop sequencial

**Localização:** `Foreach Loop Container` + `DFT - 2 Busca e Carga Posição`

O package faz uma chamada HTTP separada para cada veículo. Com 300+ veículos e latência típica de 1-2s por chamada HTTP, o tempo total é de 5-10 minutos ou mais. Não há paralelismo.

**Sugestão:** Verificar se a API Sascar oferece método de busca em lote (ex.: múltiplos idVeiculo em uma chamada), ou implementar paralelismo via múltiplos packages/tasks.

---

### [ALTA-04] Error Output do Destination desconectado

**Localização:** `ADO NET Destination` no DFT-2

O Error Output existe mas não está conectado. Erros de inserção (ex.: violação de constraint, overflow de tipo) causarão falha do package sem log de linha problemática.

**Sugestão:** Conectar o Error Output a tabela de log ou arquivo de erro.

---

### [MEDIA-01] Conexões definidas mas não utilizadas

**Localização:** `ConnectionManagers`

Três connection managers estão definidas mas não são utilizadas pelo fluxo principal:
- `CCM_JornadaMotoristaCache` (CACHE com colunas de jornada do motorista)
- `Excel Connection Manager` (arquivo local do desenvolvedor: `C:\Users\luiz.costa\Desktop\Output_Sascar1.xlsx`)
- `HTTP Connection Manager` (WSDL Sascar — o script usa HttpWebRequest direto)

**Sugestão:** Remover conexões não utilizadas para simplificar o package.

---

### [MEDIA-02] Path absoluto de arquivo na máquina do desenvolvedor

**Localização:** `Excel Connection Manager`

`C:\Users\luiz.costa\Desktop\Output_Sascar1.xlsx` — caminho absoluto que só funciona na máquina `D2-WV47-DB01` com usuário `luiz.costa`.

**Sugestão:** Remover ou parametrizar com variável de ambiente.

---

### [MEDIA-03] Typo no namespace do Script Task de Log

**Localização:** `ScriptMain.cs` de `SCR - Log de Progresso`

O namespace declarado é `ST_4bd4d3a8dbd81487e955a2121201cdf7c` (com `3a8dbd8` e `e955`) enquanto o nome do assembly/projeto é `ST_4bd4d43a8db1487e855a2121201cd75c` (com `43a8db1` e `855a`). Os bytes do GUID estão trocados no namespace.

**Impacto:** Não impede execução (SSIS usa o DLL compilado), mas é uma inconsistência que pode causar confusão.

---

### [MEDIA-04] Cache de Jornada de Motorista aparentemente incompleto

**Localização:** `CCM_JornadaMotoristaCache`

Este cache foi definido com colunas `descricaoEventoTempoDirecao`, `dataInicio`, `idMotorista` (index key), sugerindo que havia um Lookup planejado para enriquecer os dados de motorista. Não está conectado a nada.

**Sugestão:** Verificar se a funcionalidade de jornada do motorista foi cancelada ou ainda será implementada.

---

### [BAIXA-01] Timeout de 30s no Destination pode ser insuficiente para alta carga

**Localização:** `ADO NET Destination` no DFT-2 — `CommandTimeout = 30`

Em cenários de servidor DW sob carga, 30s pode não ser suficiente para inserções em lote.

---

### [BAIXA-02] Sem auditoria de carga

Não há registro de timestamp de execução, total de veículos processados, ou falhas individuais por veículo em tabela de controle.

---

### [BAIXA-03] Quantidade máxima de veículos hardcoded

**Localização:** Script `main.cs` — `<quantidade>3000</quantidade>`

O valor `3000` está hardcoded. Se a frota crescer além de 3.000 veículos, veículos serão perdidos.

**Sugestão:** Parametrizar via variável SSIS ou implementar paginação com múltiplas chamadas.

---

## Checklist de Melhoria

- [ ] Mover credenciais de API para variáveis protegidas
- [ ] Corrigir tipos das colunas na tabela `DW_Frota_UltimaPosicao1`
- [ ] Avaliar chamada em lote à API para reduzir tempo de execução
- [ ] Conectar Error Outputs a destinos de log
- [ ] Remover ou documentar connection managers não utilizadas
- [ ] Remover path absoluto do Excel Connection Manager
- [ ] Corrigir typo no namespace do ScriptMain do Log
- [ ] Parametrizar quantidade máxima de veículos
- [ ] Adicionar auditoria de carga
