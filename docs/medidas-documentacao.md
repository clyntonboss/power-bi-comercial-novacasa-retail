# Documentação das Medidas: Projeto Comercial — NovaCasa Retail

Este documento lista todas as medidas criadas no modelo Power BI, suas regras de negócio, dependências e retornos esperados.

---

# Medidas de Faturamento

## faturamento

```DAX
-- Medida: faturamento
-- Descrição: Calcula o faturamento total de vendas
--            considerando o contexto de filtros aplicado no relatório
--
-- Tabela origem:
--     fVendas
--
-- Regra de negócio:
--     Soma os valores da coluna faturamento da tabela de vendas,
--     representando a receita total gerada pelas vendas no contexto
--     atual de filtros do relatório.
--
-- Dependência:
--     Coluna fVendas[faturamento]
--
-- Retorno:
--     Valor numérico representando o faturamento total
--
-- Observação:
--     COALESCE é utilizado para evitar retorno BLANK() em visuais.

VAR _Resultado =
    SUM(
        fVendas[faturamento]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Calcula o faturamento total gerado pelas vendas.

---

## faturamento_vendedor

```DAX
-- Medida: faturamento_vendedor
-- Descrição: Calcula o faturamento total associado aos vendedores
--            considerando o contexto de filtros aplicado no relatório
--
-- Tabela origem:
--     fVendas
--
-- Regra de negócio:
--     Soma os valores da coluna faturamento da tabela de vendas
--     utilizando um iterador para avaliar o faturamento no contexto
--     de cada vendedor.
--
-- Dependência:
--     Coluna fVendas[faturamento]
--
-- Retorno:
--     Valor numérico representando o faturamento total por vendedor

VAR _Resultado =
    SUMX(
        fVendas,
        fVendas[faturamento]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Calcula o faturamento associado a cada vendedor.

---

## maior_faturamento

```DAX
-- Medida: maior_faturamento
-- Descrição: Calcula o maior faturamento obtido entre os vendedores
--
-- Dependência:
--     Medida [faturamento_vendedor]
--     Coluna fVendas[id_vendedor]

VAR _Resultado =
    MAXX(
        ALL(
            fVendas[id_vendedor]
        ),
        [faturamento_vendedor]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Retorna o maior faturamento entre todos os vendedores.

---

## menor_faturamento

```DAX
-- Medida: menor_faturamento
-- Descrição: Calcula o menor faturamento obtido entre os vendedores
--
-- Dependência:
--     Medida [faturamento_vendedor]
--     Coluna fVendas[id_vendedor]

VAR _Resultado =
    MINX(
        ALL(
            fVendas[id_vendedor]
        ),
        [faturamento_vendedor]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Retorna o menor faturamento entre todos os vendedores.

---

# Medidas de Volume de Vendas

## quantidade_vendida

```DAX
-- Medida: quantidade_vendida
-- Descrição: Calcula a quantidade total de produtos vendidos

VAR _Resultado =
    SUM(
        fVendas[quantidade]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Soma a quantidade total de itens vendidos.

---

## quantidade_produtos

```DAX
-- Medida: quantidade_produtos
-- Descrição: Calcula a quantidade total de produtos vendidos

VAR _Resultado =
    SUM(
        fPedidos[quantidade_produto]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Soma a quantidade total de produtos vendidos.

---

# Medidas de Clientes

## quantidade_clientes

```DAX
-- Medida: quantidade_clientes
-- Descrição: Calcula a quantidade total de clientes cadastrados

VAR _Resultado =
    COUNTROWS(
        dClientes
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Conta o número de clientes cadastrados.

---

## quantidade_lojas

```DAX
-- Medida: quantidade_lojas
-- Descrição: Calcula a quantidade de cidades distintas

VAR _Resultado =
    DISTINCTCOUNT(
        dClientes[cidade]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Conta o número de cidades distintas associadas aos clientes.

---

# Medidas de Transações

## transacoes

```DAX
-- Medida: transacoes
-- Descrição: Calcula a quantidade total de transações realizadas

VAR _Resultado =
    DISTINCTCOUNT(
        fVendas[numero_nf]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Conta o número de notas fiscais distintas.

---

## transacoes_feminino

```DAX
-- Medida: transacoes_feminino
-- Descrição: Calcula transações realizadas por clientes do gênero feminino

VAR _Resultado =
    CALCULATE(
        [transacoes],
        dClientes[genero] = "F"
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Quantidade de transações realizadas por clientes do gênero feminino.

---

## transacoes_masculino

```DAX
-- Medida: transacoes_masculino
-- Descrição: Calcula transações realizadas por clientes do gênero masculino

VAR _Resultado =
    CALCULATE(
        [transacoes],
        dClientes[genero] = "M"
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Quantidade de transações realizadas por clientes do gênero masculino.

---

# Medidas de Ticket e Meta

## ticket_medio

```DAX
-- Medida: ticket_medio
-- Descrição: Calcula o ticket médio por cliente

VAR _Resultado =
    DIVIDE(
        [faturamento],
        [quantidade_clientes]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Calcula o faturamento médio por cliente.

---

## meta_total

```DAX
-- Medida: meta_total
-- Descrição: Calcula o total de metas definidas

VAR _Resultado =
    SUM(
        dMeta[meta]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Soma os valores de meta definidos na tabela de metas.

---

## meta

```DAX
-- Medida: meta
-- Descrição: Calcula o percentual de atingimento da meta

VAR _Resultado =
    DIVIDE(
        [faturamento],
        [meta_total]
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Percentual de cumprimento da meta de faturamento.

---

# Medidas de Destaque de Valores (Data Storytelling)

## menor_maior_faturamento

```DAX
VAR _MenorFaturamento =
    MINX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [faturamento]
    )

VAR _MaiorFaturamento =
    MAXX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [faturamento]
    )

VAR _Resultado =
    IF(
        [faturamento] = _MaiorFaturamento
        || [faturamento] = _MenorFaturamento,
        [faturamento]
    )

RETURN
    _Resultado
```

Descrição: Retorna o valor do faturamento apenas quando corresponde ao maior ou menor valor do período.

---

## menor_maior_quantidade_vendida

```DAX
VAR _MenorQuantidade =
    MINX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [quantidade_vendida]
    )

VAR _MaiorQuantidade =
    MAXX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [quantidade_vendida]
    )

VAR _Resultado =
    IF(
        [quantidade_vendida] = _MaiorQuantidade
        || [quantidade_vendida] = _MenorQuantidade,
        [quantidade_vendida]
    )

RETURN
    _Resultado
```

Descrição: Retorna o valor da quantidade vendida apenas quando corresponde ao maior ou menor valor do período.

---

# Medidas de Cores para Destaque Visual

## cores_menor_maior_faturamento

```DAX
VAR _MenorFaturamento =
    MINX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [faturamento]
    )

VAR _MaiorFaturamento =
    MAXX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [faturamento]
    )

VAR _Resultado =
    SWITCH(
        TRUE(),
        [faturamento] = _MaiorFaturamento, "#33CC33",
        [faturamento] = _MenorFaturamento, "#FF0000",
        "#808080"
    )

RETURN
    _Resultado
```

Descrição: Define cores para destacar maior e menor faturamento no gráfico.

---

## cores_menor_maior_quantidade_vendida

```DAX
VAR _MenorQuantidade =
    MINX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [quantidade_vendida]
    )

VAR _MaiorQuantidade =
    MAXX(
        ALLSELECTED(
            dCalendario[mes_abreviado],
            dCalendario[mes_numero]
        ),
        [quantidade_vendida]
    )

VAR _Resultado =
    SWITCH(
        TRUE(),
        [quantidade_vendida] = _MaiorQuantidade, "#33CC33",
        [quantidade_vendida] = _MenorQuantidade, "#FF0000",
        "#808080"
    )

RETURN
    _Resultado
```

Descrição: Define cores para destacar maior e menor quantidade vendida.

---

# Medidas de Estrutura Organizacional

## quantidade_vendedores

```DAX
-- Medida: quantidade_vendedores
-- Descrição: Calcula a quantidade total de vendedores cadastrados

VAR _Resultado =
    COUNTROWS(
        dVendedores
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Conta o número total de vendedores cadastrados.
