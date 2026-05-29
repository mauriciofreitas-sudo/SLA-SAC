# Transformações — ETL_Auditoria_CTe

## Resumo

Este package e **passthrough puro**: nao ha nenhum componente de transformacao (Derived Column, Lookup, Data Conversion, etc.) no Data Flow Task. Os dados fluem diretamente das 3 sources para os 3 destinos sem modificacao alguma.

---

## Componentes do Data Flow — Tarefa Fluxo de Dados

```
+-------------------------+          +------------------------+
|  Origem Auditoria Cte   |          |    Destino OLE DB      |
|  vw_Auditoria_CTe_      | -------> |  stg_Auditoria_CTe     |
|  Michelin               |          |  (53 col.)             |
|  (79 col. source)       |          |                        |
+-------------------------+          +------------------------+
         |
         v (Error Output — nao conectado)

+-------------------------+          +------------------------+
|  Origem Cte Produto     |          |  Destino OLE DB 1      |
|  vw_AuditoriaCTe_Prod   | -------> |  Auditoria_CTe_Cod_Prod|
|  (5 col.)               |          |  (4 col.)              |
+-------------------------+          +------------------------+
         |
         v (Error Output — nao conectado)

+-------------------------+          +------------------------+
|  Origem Prod_Natureza   |          |  Destino OLE DB 2      |
|  vw_AuditoriaCTe_Prod   | -------> |  Auditoria_CTe_Cod_    |
|  _v2 (6 col.)           |          |  Prod_v2 (6 col.)      |
+-------------------------+          +------------------------+
         |
         v (Error Output — nao conectado)
```

> Observacao: Os 3 fluxos dentro do Data Flow Task sao independentes e executam em paralelo.

---

## Pré-processamento (SQL Tasks antes do Data Flow)

| Ordem | Task | SQL Executado | ThreadHint |
|-------|------|---------------|------------|
| 1 (paralelo) | Delete CTe_Prod | `Delete from [dbo].[Auditoria_CTe_Cod_Prod]` | 0 |
| 1 (paralelo) | Delete Prod_Natureza | `Delete from [Auditoria_CTe_Cod_Prod_v2]` | 1 |
| 1 (paralelo) | Tarefa Auditoria CTe | `Delete from [dbo].[stg_Auditoria_CTe]` | 2 |

> Os ThreadHints (0, 1, 2) com LogicalAnd=True significa que todas as 3 tasks precisam concluir com sucesso antes do Data Flow iniciar.

---

## Mapeamento de Tipos de Dados — Fluxo Principal (stg_Auditoria_CTe)

| Tipo Source (SSIS) | Tipo Destino (SSIS) | Observacao |
|--------------------|---------------------|------------|
| i4 | i4 | Passthrough direto |
| str (cp1252) | str (cp1252) | Passthrough direto — ANSI, nao Unicode |
| numeric(14,4) | numeric(14,4) | Passthrough direto |
| dbTimeStamp | dbTimeStamp | Passthrough direto |

> Atencao: O package usa `str` (ANSI/cp1252) e nao `wstr` (Unicode). Isso e tipico de conexoes OLEDB antigas. Dados com caracteres fora do cp1252 podem ser corrompidos silenciosamente.

---

## Diferenca entre as Sources 1 e 2 de Produto

| Aspecto | `vw_AuditoriaCTe_Prod` (v1) | `vw_AuditoriaCTe_Prod_v2` (v2) |
|---------|----------------------------|--------------------------------|
| Destino | `Auditoria_CTe_Cod_Prod` | `Auditoria_CTe_Cod_Prod_v2` |
| Colunas | 5 (EmpresaNF, DoctFiscalNF, TpdoctfiscalNF, CdNaturezaNF, CdProdCliente) | 6 (+CdProduto, DsNatureza, CdNatureza) |
| Proposito | Codigo de produto por CT-e | Produto com descricao de natureza |
