# ETL_Romaneios1 — Transformações

## Resumo

Este package é **passthrough puro**. Não há nenhum componente de transformação no pipeline de dados:
- Sem Derived Column
- Sem Data Conversion
- Sem Lookup
- Sem Conditional Split
- Sem Aggregate

O dado flui diretamente da origem (`ADO NET Source`) para o destino (`ADO NET Destination`) sem qualquer modificação de coluna.

---

## Diagrama ASCII do Data Flow

```
[ADO NET Source]          [ADO NET Destination]
Ficha_de_Viagem_Geral  -->  fFichaViagem
169.57.181.231             10.100.86.89
SOFTRAN_TRANSLUTE          DWGrupolc
(51 colunas)        ---->  (51 colunas mapeadas)
```

---

## Pré-processamento SQL (antes do Data Flow)

**Task: Truncate fFicha**

```sql
DELETE FROM dbo.fFichaViagem
```

Essa instrução é executada via conexão OLEDB (`10.100.86.89.DWGrupolc.sqldba1`) com ThreadHint=0.

**Observação crítica:** O package define uma variável `User::StartDate` com valor padrão `01/01/2025` e cria um `ParameterBinding` para o parâmetro `0` nessa task. No entanto, o SQL `DELETE FROM dbo.fFichaViagem` não contém nenhum parâmetro `?` — o ParameterBinding está configurado mas não tem efeito prático. Provavelmente é um resíduo de uma versão anterior que tinha `WHERE DtEmissao >= ?`.

---

## Mapeamento de Tipos

Todos os campos são mapeados diretamente sem conversão explícita. O ADO.NET source usa `AllowImplicitStringConversion = true`, o que permite conversões implícitas de tipos não-string para string quando necessário.

| Tipo na Fonte (SQL Server) | Tipo SSIS          | Observação                            |
|----------------------------|--------------------|---------------------------------------|
| int                        | i4                 | Mapeamento direto                     |
| datetime / datetime2       | dbTimeStamp        | Mapeamento direto                     |
| decimal / numeric          | numeric (p14, s4)  | Mapeamento direto                     |
| varchar / nvarchar         | wstr               | Mapeamento direto                     |
