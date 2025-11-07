Título
======

Subtítulo
---------

Para criar um parágrafo, basta escrever um texto contínuo, sem pular linhas.

Você também pode criar

1. listas;

2. ordenadas,

assim como

* listas;

* não-ordenadas

e imagens. Lembre que todas as imagens devem estar em uma subpasta *img*.

![](logo.png)

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b |
|----------|----------|
| 1        | 2        |

Ao longo de um texto, você pode usar *itálico*, **negrito**, {red}(vermelho) e
[[tecla]]. Também pode usar uma equação LaTeX: $f(n) \leq g(n)$. Se for muito
grande, você pode isolá-la em um parágrafo.

$$\lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} \leq 1$$

Para inserir uma animação, use `md :` seguido do nome de uma pasta onde as
imagens estão. Essa pasta também deve estar em *img*.

:bubble

Você também pode inserir código, inclusive especificando a linguagem.

``` py
def f():
    print('hello world')
```

``` c
void f() {
    printf("hello world\n");
}
```

Se não especificar nenhuma, o código fica com colorização de terminal.

```
hello world
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!


??? Exercício

Este é um exemplo de exercício, entre `md ???`.

::: Gabarito
Este é um exemplo de gabarito, entre `md :::`.
:::

???





Needleman-Wunsch
---------------------

O algoritmo é baseado em pontuação. Cada correspondência entre caracteres recebe uma pontuação positiva, enquanto as não-correspondências e gaps recebem pontuações negativas (penalidades). O objetivo do algoritmo é maximizar a pontuação total do alinhamento.

??? Reflexão

Quais são as entradas e saídas do algoritmo de Needleman-Wunsch?

::: Gabarito

Entradas:
- Duas sequências a serem alinhadas.
- Pontuações para correspondências, não-correspondências e gaps (exemplo: +1 para correspondência, -1 para não-correspondência, -2 para gap).

Saídas:
- Duas sequências (de mesmo comprimento) alinhadas de forma ótima.
:::

???

Vamos agora refletir como o algoritmo funciona. 

??? Atividade



???