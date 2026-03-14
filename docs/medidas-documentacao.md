# Documentação das Medidas: Projeto Comercial — NovaCasa Retail

Este documento lista todas as medidas criadas no modelo Power BI, suas regras de negócio, dependências e retornos esperados.

------------------------------------------------------------------------

## Medidas de Meta

### meta_total

``` dax
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

Descrição: Calcula o valor total das metas registradas na tabela
`dMeta`.

------------------------------------------------------------------------

## Medidas de Quantidade de Vendas

### quantidade_vendida

``` dax
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

Descrição: Calcula a quantidade total de produtos vendidos.

------------------------------------------------------------------------

### menor_maior_quantidade_vendida

``` dax
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

RETURN
    IF(
        [quantidade_vendida]=_MaiorQuantidade||
        [quantidade_vendida]=_MenorQuantidade,
        [quantidade_vendida]
    )
```

Descrição: Destaca os valores correspondentes ao maior e ao menor volume
de vendas dentro do período selecionado.

------------------------------------------------------------------------

## Medidas de Indicadores Comerciais

### ticket_medio

``` dax
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

Descrição: Calcula o ticket médio de vendas, dividindo o faturamento
total pela quantidade de clientes.

------------------------------------------------------------------------

## Medidas de Transações

### transacoes

``` dax
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

Descrição: Conta o número total de notas fiscais distintas registradas
nas vendas.

------------------------------------------------------------------------

### transacoes_feminino

``` dax
VAR _Resultado =
    CALCULATE(
        [transacoes],
        dClientes[genero]="F"
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Calcula o total de transações realizadas por clientes do
gênero feminino.

------------------------------------------------------------------------

### transacoes_masculino

``` dax
VAR _Resultado =
    CALCULATE(
        [transacoes],
        dClientes[genero]="M"
    )

RETURN
    COALESCE(
        _Resultado,
        0
    )
```

Descrição: Calcula o total de transações realizadas por clientes do
gênero masculino.

------------------------------------------------------------------------

## Medidas de Estrutura Comercial

### quantidade_vendedores

``` dax
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

Descrição: Calcula a quantidade total de vendedores cadastrados na
dimensão `dVendedores`.
