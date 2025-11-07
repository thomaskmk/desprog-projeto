Algoritmo de Needleman-Wunsch
======

O algoritmo é um método clássico para o alinhamento global de sequências biológicas.  Ele utiliza programação dinâmica para encontrar a melhor correspondência entre duas sequências, considerando penalidades para gaps e recompensas para correspondências.

Antes de avançarmos, entenda sequência como uma **cadeia de elementos de um tipo específico**.

Definição de alinhamento
---------------------

Um alinhamento de sequências é uma transformação que aplicamos a duas (ou mais) sequências para revelar suas regiões de similaridade, o que é particularmente útil para a biologia computacional.

A transformação consiste em **inserir caracteres de lacuna (gap)**, representados por um hífen (-), em qualquer uma das sequências. O objetivo dessa transformação é fazer com que ambas as sequências terminem com o **exatamente o mesmo comprimento**, permitindo uma comparação **caractere a caractere** em cada posição.

<div style="text-align: center;">
<img src="exemplos/alinhamento.jpg" width="500"/>
</div>


??? Exemplo

Considere as seguintes sequências de DNA:
- Sequência 1: ATCGAGC
- Sequência 2: ATGAC

Forneça um possível alinhamento para as sequências.

::: Dica
Busque partes similares entre as sequências e use gaps (-) para alinhá-las.
:::

::: Gabarito

```
ATCGAGC
AT-GA-C
```

!!! Lembre-se
Após o alinhamento, sempre teremos duas sequências de mesmo comprimento.
!!!
:::

???

## Múltiplas Possibilidades
Talvez você tenha se perguntado por citamos **"...um possível alinhamento..."**. É crucial entender que, para qualquer par de sequências, existem múltiplos alinhamentos possíveis. A **forma como inserimos os gaps** muda o alinhamento.

??? Exemplo
Para as mesmas sequências do exemplo anterior, podemos propor o seguinte alinhamento:
```
ATCGAGC--
AT-G-A--C
```
???
Note que as sequências continuam com o **mesmo tamanho**, mas alinhadas de maneira diferente. O ponto chave aqui é entender que alinhar as sequências é encontrar a disposição que gera o **maior número de correspondências** quando comparamos elemento a elemento.

??? Exercício
Considere as seguintes sequências de DNA:
- Sequência 1: GATTA
- Sequência 2: GCA

Forneça três alinhamentos **diferentes** e válidos para este par.

::: Gabarito
Existem dezenas de possibilidades. Aqui estão alguns exemplos:
```
GATTA
GC-A-

GATTA
G-C-A

G-ATTA
G-C--A
```
:::
???

Para sequências curtas como essas, podemos fazer o alinhamento manualmente. Mas se a sequência for maior, fazer "no olho" se torna inviável. Precisamos de uma metodologia sistemática para encontrar o melhor alinhamento possível.

## Metrificando Alinhamentos: O Sistema de Pontuação
!!! Atencão
Antes de iniciar, é crucial entender que no processo de alinhamento, comparam-se os elementos da sequência **posição por posição**. Ou seja, se colocarmos uma sequência "em cima" da outra, comparamos **verticalmente** os elementos na primeira posição, depois na segunda, enfim...
<div style="text-align: center;">
<img src="exemplos/posicao-posicao.png" width="350"/>
</div>
!!!

Na seção anterior, vimos que existem muitos alinhamentos possíveis. Como podemos **decidir objetivamente** qual alinhamento é "melhor" ou "mais significativo"?

Fazemos isso através de um **sistema de pontuação** (ou custo). Atribuímos um **valor numérico** a cada tipo de comparação, posição por posição, onde existem três possibilidades:

- **Match (Correspondência)**: Os caracteres na mesma posição são idênticos (ex: A e A).

- **Mismatch (Não-correspondência)**: Os caracteres na mesma posição são diferentes (ex: T e C).

- **Gap (Lacuna)**: Um caractere é alinhado com um gap (-).

Exemplos:
```
A
    (match)
A
----------
A
    (mismatch)
G
----------
A
    (gap)
-
```

Os valores de exatos de cada possibilidade são **parâmetros definidos pelo usuário** ou pela aplicação específica. Por exemplo, podemos definir:
- **Match**: +1
- **Mismatch**: -1
- **Gap**: -2

O custo total é simplesmente a soma dos valores atribuídos a cada posição do alinhamento. Dessa forma, o "melhor alinhamento" é aquele que resulta na **maior pontuação total**.

??? Exercício: calculando custos
Considerando as sequências do exemplo anterior:
```
GATTA
GCA
```

Calcule o custo total para os seguintes alinhamentos de acordo com o sistema definido acima e responda: qual deles é o melhor alinhamento?

**Alinhamento 1:**
```
GATTA
GC-A-
```

**Alinhamento 2:**
```
GATTA
G-C-A
```

::: Gabarito
Alinhamento 1:
```
Posição: 1   2   3   4   5
-----------------------------
Seq A:   G   A   T   T   A
Seq B:   G   C   -   A   -
-----------------------------
Score:  +1  -1  -2  -1  -2
```
**Score total: -5**

Alinhamento 2:
```
Posição: 1   2   3   4   5
-----------------------------
Seq A:   G   A   T   T   A
Seq B:   G   -   C   -   A
-----------------------------
Score:  +1  -2  -1  -2  +1
```
**Score total: -3**

O melhor alinhamento é o **Alinhamento 2**, com um score total de -3.

:::
???



---

Alinhamento por Recursão
---------------------
O alinhamento de sequências pode ser abordado de forma recursiva, onde o problema maior é dividido em **subproblemas menores**.

Para alinhar duas sequências, podemos começar do fim: comparar os últimos caracteres e decidir o que fazer com eles.
Depois, repetimos o mesmo raciocínio para o restante. É assim que o algoritmo divide o problema em partes menores.

??? Atividade 
Se temos duas sequências de tamanhos diferentes, como poderíamos comparar os últimos caracteres de cada uma delas?

::: Dica
Observe os seguintes exemplos de alinhamento e foque nos últimos caracteres: você enxerga algum padrão?

:exemplos


:::

::: Gabarito
Precisamos considerar a inserção de gaps (-) em uma das sequências para que elas tenham o mesmo tamanho. Dessa forma, devemos considerar as seguintes combinações ao dividir o problema:

- Comparar o caractere de ambas as sequências (caso ambas as sequências tenham o mesmo tamanho).
- Comparar o caractere da primeira sequência com um gap na segunda sequência (caso a primeira sequencia seja maior que a segunda).
- Comparar um gap na primeira sequência com o caractere da segunda sequência (caso a segunda sequência seja maior que a primeira).

Mas agora surge uma nova questão:
essas três opções não são equivalentes — algumas trazem correspondência, outras causam penalidade.
Para escolher a melhor, precisamos atribuir pontuações a cada tipo de comparação.

!!! Importante
Para decidir qual dessas três opções é a melhor, precisamos atribuir uma pontuação a cada uma delas.

- Match → ganho
- Mismatch → perda
- Gap → penalidade

Essa pontuação nos ajuda a quantificar o quão bom é um alinhamento específico, e será fundamental para o funcionamento do algoritmo de Needleman-Wunsch.

!!!

:::

???

Muito abstrato? Vamos exemplificar.

Considere as seguintes sequências:
- Sequência 1: ATC
- Sequência 2: AG

Para encontrar o alinhamento ótimo, o algoritmo funciona "do fim para o começo". Ele se pergunta: qual é a melhor forma de alinhar os últimos caracteres (C e G)?

Existem três possibilidades para a **última coluna** do alinhamento:

1. Alinhar **C** com **G** (um *mismatch*). O resto do problema (o subproblema) é alinhar **AT** com **A**.
2. Alinhar **C** com um **gap (-)**. O resto do problema é alinhar **AT** com **AG**.
3. Alinhar um **gap (-)** com **G**. O resto do problema é alinhar **ATC** com **A**.

O algoritmo então escolhe a opção que resulta na maior pontuação total (somando o *score* da escolha atual com o *score* ótimo do subproblema que ela gerou). Ele repete esse processo recursivamente até que ambas as sequências "acabem" (cheguem ao início).

Talvez você esteja se questionando: a recursão é uma boa abordagem para esse problema?

??? Atividade

Considerando *m* e *n* os tamanhos das duas sequências a serem alinhadas, determine a **complexidade de tempo do algoritmo recursivo**.

::: Dica
Considere que na chamada recursiva pelo menos uma das sequências será reduzida. Dessa forma, no pior caso, são quantos passos até o caso base?
:::

::: Gabarito
No pior caso, a chamada recursiva reduz apenas uma das sequências por vez (ou seja, alinha o caractere de uma com um gap na outra). Nesse caso, até o caso base (ambas as sequências vazias), são ***m* + *n* chamadas**.  

Para cada chamada, existem 3 possibilidades, que geram **3 chamadas recursivas**. Portanto, a complexidade do algoritmo é **$O(3^{m+n})$**.

O diagrama ilustra a recursão:

<div style="text-align: center;">
<img src="exemplos/diagrama-recursao.png" width="500"/>
</div>


!!! Atenção
**Subproblemas Sobrepostos**

O algoritmo acaba calculando o score para o *mesmo par de subsequências* múltiplas vezes, em diferentes "galhos" da árvore de recursão.

Para sequências longas, o número de cálculos repetidos cresce exponencialmente, tornando a abordagem recursiva pura completamente impraticável.
!!!
:::

???

Algoritmo de Needleman-Wunsch
---
Como vimos na seção anterior, a complexidade do algoritmo recursivo torna-o impraticável para sequências maiores.

Precisamos, portanto, de outra forma menos custosa para fazer o alinhamento, e é aí que entra o algoritmo de Needleman-Wunsch.

Para que nós tenhamos um norte de como o algorítmo funciona, é extremamente importante que ter um entendimento claro dessas premissas: 

- Trabalhamos com uma matriz *m* x *n* 

- Definimos os valores das bordas com valores dos pesos de um gap, em que a cada celula, aumentamos o peso.

- A ideia é calcular a pontuação das comparações entre as palavras usando **duas** regras: 


1. Para comparar gaps, analisamos as celulas a esquerda e ao topo da celula atual. 

2. Para calcular matches e mismatches, usamos as celulas da diagonal da celula atual.


!!!
Talvez, você tenha ficado confuso. Faz sentido, não é trivial. 
O exemplo pode esclarecer melhor:
!!!

<div style="text-align: center;">
<img src="exemplos/calculo.png" width="300"/>
</div>


Mas espere, e quanto ao segundo bullet point, ainda não ficou claro, né?

Infelizmente, você faz engenharia e nada vem tão simples. 

Assim, vou propor duas reflexões especialmente importantes: 

??? Reflexão 1

Qual o motivo de existirem essas três setas que vem da diagonal, da esquerda e do topo, como mostrado na ultima figura para o cálculo da pontuação?


::: Dica
São três possibilidades, três pontuações, e cada recursão tinha três ramos...
:::

::: Gabarito
O motivo para fazermos isso é o fato de estarmos buscando pela maior pontuação dentro de cada comparação entre gap vs letra, letra vs letra, letra vs gap. Assim, já ganhamos uma intuição do porque esse modelo é mais eficiente.
::: 

???

??? Reflexão 2

Qual o motivo de inicializarmos a matriz de alinhamento com esses valores nas linhas e nas colunas? 

<div style="text-align: center;">
<img src="exemplos/pesos.png" width="300"/>
</div>


::: Dica
Qual o custo de alinhar uma sequência qualquer de *m* caracteres com uma sequência vazia?
:::

::: Gabarito 
A inicialização da matriz com penalidades de gap cumulativas não é arbitrária. Ela é a solução de Programação Dinâmica para os casos base.
:::

???

??? Exemplo
A sequência de imagens a seguir representa o preenchimento da matriz das sequências **ATCGAGC** e **ATGAC** utilizando o algoritmo de Needleman-Wunsch com as seguintes pontuações:

- Match: +1
- Mismatch: -1
- Gap: -2

:pontuacoes
???