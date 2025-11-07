Algoritmo de Needleman-Wunsch
======

O algoritmo de estudo é um método clássico para o alinhamento global de sequências biológicas.  Ele utiliza programação dinâmica para encontrar a melhor correspondência entre duas sequências, considerando penalidades para gaps e recompensas para correspondências.

Antes de avançarmos para sua definição rigorosa vamos entender o que é uma sequência.

Definição de alinhamento
---------------------

Um alinhamento de sequências é uma transformação que aplicamos a duas (ou mais) sequências para revelar suas regiões de similaridade, o que é particularmente útil para a biologia computacional.

A transformação consiste em **inserir caracteres de lacuna (gap)**, representados por um hífen (-), em qualquer uma das sequências. O objetivo dessa transformação é fazer com que ambas as sequências terminem com o **exatamente o mesmo comprimento**, permitindo uma comparação **caractere a caractere** em cada posição.

O alinhamento de sequências é o processo de dispor duas ou mais sequências, uma sobre a outra, inserindo lacunas (gaps) onde necessário. O objetivo dessa organização é maximizar a quantidade de **caracteres iguais em cada posição**, que visualmente nos permite **identificar regiões de similaridade**. 

Para cada sequência que caracteres, podemos fazer varios alinhamentos possíveis. 
Por exemplo, para as sequências de DNA, **GATTACA** **GATCA**, observe os diferentes alinhamentos possíveis:

: diferentes_alinhamentos

Dessa forma, já ficou mais visualizavel que existem diversas maneiras de alinhar duas sequências.


??? Exemplo

Considere as seguintes sequências de DNA:
- Sequência 1: ```A T C G A G C```
- Sequência 2: ```A T G A C```

Se você pudesse alinhar essas duas sequências de três maneiras diferentes, como você faria?

::: Dica
Lembre-se, um alinhamento é válido desde que as duas sequências resultantes tenham o **mesmo comprimento** final. Tente inserir os gaps {red}(-) em locais diferentes.
:::

::: Gabarito

Existem muitas formas válidas! O importante é que ambas as sequências terminem com o mesmo comprimento. Aqui estão três exemplos possíveis:

**Opção 1:**
- Sequência 1: A T C G A G C
- Sequência 2: A T {red}(-) G A {red}(-) C

**Opção 2:**
- Sequência 1: A T C G A G C
- Sequência 2: A T G A C {red}(-) {red}(-)

**Opção 3:**
- Sequência 1: A T C G A G C 
- Sequência 2: {red}(-) {red}(-) A T G A C

!!! Atenção
Note que todas as 3 opções são alinhamentos **válidos**. Em cada uma delas, as duas sequências terminam com o mesmo comprimento (neste caso, 7).

Isso nos mostra que existem muitos alinhamentos possíveis, o que nos levará à próxima pergunta: "Como definimos qual deles é o **melhor**?"
!!!
:::
???

Bom, agora que está claro o que é um alinhamento e como ele é feito, é necessário explicar a maneira que o algoritmo ultiliza a matemática a seu favor para encontrar o melhor alinhamento possível dentro dessas sequencias.

Isso é feito  através de um sistema de pontuação genérico. Basicamente olhamos caráctere por caráctere e atribuímos uma pontuação baseada em três critérios:

- **Match** (Correspondência): quando os caracteres são iguais. Exemplo: A vs A
Cenário em que somamos um valor positivo à pontuação do alinhamento.

- **Mismatch** (Diferença): quando os caracteres são diferentes. Exemplo: A vs G
Pior cenário em que subtraímos um valor significativo da pontuação do alinhamento.

- **Gap** (Lacuna): quando um caractere é alinhado com um gap. Exemplo: A vs -
Cenário considerado ruim e também que subtraímos um valor moderado da pontuação do alinhamento.

Agora, você já é capaz de entender como o algoritmo avalia cada alinhamento possível entre duas sequências. Mas você pode estar se perguntando como o algoritmo escolhe esse sistema de pontuação. Esse ponto é bastante importante, pois pode causar confusão. 


Para que os pontos sejam definidos, é importante esclarecer que são **apenas** parâmetros, ou seja, são apenas valores que podemos ajustar da maneira que quisermos servindo para qualquer caso, com quaisquer valores. No entanto, precisamos ter em mente que:

- {green}(**Matches**) devem ter pontuações {green}(**positivas**).
- {yellow}(**Gaps**) devem ter pontuações negativas, mas {yellow}(menos negativas) que mismatches.
- {red}(**Mismatches**) devem ter as pontuações {red}(mais negativas).


Por exemplo, uma configuração comum de pontuação é:
- Match: +2
- Mismatch: -3
- Gap: -1

Então, um exemplo de uma soma seria: 

??? Atividade

Considere as sequências `Seq 1: AGTC` e `Seq 2: ATC`.
Para esta atividade, use o seguinte sistema de pontuação:
* {green}(**Match**): +2
* {yellow}(**Gap**): -1
* {red}(**Mismatch**): -3


Calcule a pontuação total para cada um dos três alinhamentos abaixo.

**Alinhamento A:**

|| A | G | T | C |
|:---:|:---:|:---:|:---:|
|| **A** | **-** | **T** | **C** |

**Alinhamento B:**

|| A | G | T | C |
|:---:|:---:|:---:|:---:|
|| **A** | **T** | **C** |**-** |


**Alinhamento C:**

|| A | G | T | C |
|:---:|:---:|:---:|:---:|
|| **-** | **A**| **T** | **C** |

::: Dica
A soma vai ser calculada pela comparação de cada caractere.
:::

::: Gabarito

* **Alinhamento A (```AGTC``` vs ```A-TC```)**
    * A vs A (Match): +2
    * G vs - (Gap): -1
    * T vs T (Match): +2
    * C vs C (Match): +2
    * **Total:** (+2) + (-1) + (+2) + (+2) = **+5**

* **Alinhamento B (```AGTC``` vs ```ATC-```)**
    * A vs A (Match): +2
    * G vs T (Mismatch): -3
    * T vs C (Mismatch): -3
    * C vs - (Gap): -1
    * **Total:** (+2) + (-3) + (-3) + (-1) = **-5**

* **Alinhamento C (```AGTC``` vs ```-ATC```)**
    * A vs - (Gap): -1
    * G vs A (Mismatch): -3
    * T vs T (Match): +2
    * C vs C (Match): +2
    * **Total:** (-1) + (-3) + (+2) + (+2) = **0**
:::
???


??? Checkpoint

Usando o **mesmo sistema de pontuação** da atividade passada, analise os dois alinhamentos abaixo para a sequência

| Sequência 1 | A | T | C |
|:---:|:---:|:---:|:---:|
| **Sequência 2** | **A** | **G** | |


Qual dos dois alinhamentos, X ou Y, possui o **melhor custo**?

**Alinhamento X:**

|| G | A | - |
|:---:|:---:|:---:|:---:|
|| **G** | **A** | **T** |


**Alinhamento Y:**

|| - | G | A |
|:---:|:---:|:---:|:---:|
|| **G** | **A** | **T** |



::: Dica
Lembre-se, o "melhor" custo é o que resulta na **maior** pontuação final.
:::

::: Gabarito

* **Alinhamento X:** (`G` vs `G`) + (`A` vs `A`) + (`-` vs `T`) = (+2) + (+2) + (-1) = **+3**
* **Alinhamento Y:** (`-` vs `G`) + (`G` vs `A`) + (`A` vs `T`) = (-1) + (-3) + (-3) = **-7**

O **Alinhamento X** é o melhor, pois +3 é maior que -7.
:::
???

Agora, já temos uma boa intuição de como o alinhamento de sequências funciona e como a pontuação para a melhor sequência é calculada. Agora, pode estar surgindo o questionamento: "Por que usar isso é especialmente útil e por que precisamos estudar um algoritmo para isso?"


??? Reflexão

Como os cientistas desenvolvem vacinas eficazes contra vírus que estão constantemente mutando, como o SARS-CoV-2 e o Influenza?

::: Dica
O DNA dos vírus possuem regiões que mudam com frequência e outras que permanecem relativamente estáveis ao longo do tempo. Pense em como essas regiões podem influenciar o desenvolvimento de vacinas.
:::

::: Gabarito

Os pesquisadores precisam identificar quais partes do DNA do vírus permanecem iguais entre diferentes variantes (as chamadas regiões conservadas).
Essas regiões são as melhores candidatas para alvos de vacinas, pois, se não mudam, uma vacina baseada nelas continua eficaz mesmo após mutações.

O algoritmo de Needleman-Wunsch nos ajuda a alinhar sequências de DNA viral de diferentes variantes, permitindo que os cientistas identifiquem essas regiões conservadas de forma eficiente e precisa.

???

Bom, para sequências curtas como essas que trabalhamos nos exemplos, podemos fazer o alinhamento manualmente. Mas e se a sequência tivesse milhões de caractéres (no caso da biologia, bases nitrogenadas), como poderíamos automatizar esse processo?

Aqui surge a necessidade de um algoritmo eficiente para alinhar sequencias e que seja capaz de lidar com uma alta complexidade computacional. Por isso, estudamos o algoritmo de **Needleman-Wunsch**.



Alinhamento por Recursão
---------------------
A cada etapa feita nos processos passados da ultima seção, talvez não tenha ficado claro, mas durante todo o tempo, efetivamente, estavamos fazendo uma recursão. Isto é, estavamos alinhando as sequências de forma que o problema maior é dividido em **subproblemas menores**.

Para alinhar duas sequências, podemos começar do fim: comparar os últimos caracteres e decidir o que fazer com eles.
Depois, repetimos o mesmo raciocínio para o restante. É assim que o algoritmo divide o problema em partes menores.

Muito abstrato? Vamos exemplificar.

Considere as seguintes sequências:
- Sequência 1: ATC
- Sequência 2: AG

Para encontrar o alinhamento ótimo, o algoritmo funciona "do fim para o começo". Ele se pergunta: qual é a melhor forma de alinhar os últimos caracteres (C e G)?

Existem três possibilidades para a **última coluna** do alinhamento:

Considere as seguintes sequências. Estamos tentando decidir como alinhar o final delas: o {red}(C) da Sequência 1 e o {red}(G) da Sequência 2.

| Sequência 1 | A | T | C |
|:---:|:---:|:---:|:---:|
| **Sequência 2** | **A** | **G** | |


Para encontrar o alinhamento ótimo, o algoritmo funciona "do fim para o começo". Ele se pergunta: qual é a melhor forma de criar a **última coluna** do alinhamento?

Existem três possibilidades, que são efetivamente os ramos da recursão:

O algoritmo é forçado a explorar **todas as 3 possibilidades**:

---

### Ramo 1:  Alinhar Letra com Letra  ({red}(C) vs {red}(G))
O algoritmo testa alinhar os dois últimos caracteres.

* **Ação (Última Coluna):**

| Sequência 1 | A | T | {red}(C) |
|:---:|:---:|:---:|:---:|
| Sequência 2 | **A** | **G** |**-** |

* **Subproblema Gerado:** O que sobrou para alinhar foi **AT** vs **A**.

---

### Ramo 2: Alinhar Letra 1 com Gap  ({red}(C) vs {red}(-))
O algoritmo testa alinhar o caractere da Sequência 1 com um gap.

| Sequência 1 | A | T | {red}(C) |
|:---:|:---:|:---:|:---:|
| Sequência 2 | **A** | {red}(**G**) |{red}(**-**) |

* **Subproblema Gerado:** O que sobrou foi **AT** vs **AG** (pois o **G** não foi "usado").

---

### Ramo 3:Alinhar Gap com Letra 2  ({red}(-) vs {red}(G))
O algoritmo testa alinhar um gap com o caractere da Sequência 2.

* **Ação (Última Coluna):**
   
| Sequência 1 | A | T | {red}(C) |
|:---:|:---:|:---:|:---:|
| Sequência 2 | {red}(**-**)| **A** | {red}(**G**) |

* **Subproblema Gerado:** O que sobrou foi **ATC** vs **A** (pois o **C** não foi "usado").

---
<br>

???
Exercício: Assumindo que temos as seguintes pontuações:
* Match: +1 
* Mismatch: -1
* Gap: -2

Determine a pontuação total para cada uma das três opções (ramos) da recursão ao alinhar as sequências `AG` e `AG`.

::: Gabarito

Para resolver, calculamos a pontuação de cada ramo recursivo, que é o **custo da ação** +  **pontuação ótima** do subproblema.

Isso exige que primeiro saibamos as pontuações ótimas dos subproblemas (calculados recursivamente):
* `Pontuação("A" vs "A")` = +1 (pois um match é a melhor opção)
* `Pontuação("A" vs "AG")` = -1 (o alinhamento ótimo é `A-` vs `AG`, com custo `Match(A,A)` + `Gap(-,G)` = +1 + (-2) = -1)
* `Pontuação("AG" vs "A")` = -1 (o alinhamento ótimo é `AG` vs `A-`, com custo `Match(A,A)` + `Gap(G,-)` = +1 + (-2) = -1)

Agora, podemos calcular a pontuação de cada ramo do problema principal (`AG` vs `AG`):

**Ramo 1: Alinhar Letra com Letra ({red}(G) vs {red}(G))**
* **Cálculo:** `Custo(Match G,G)` + `Pontuação("A" vs "A")`
* **Pontuação:** (+1) + (+1) = **+2**

**Ramo 2: Inserir Gap na Seq. 2 ({red}(G) vs {red}(-))**
* **Cálculo:** `Custo(Gap G,-)` + `Pontuação("A" vs "AG")`
* **Pontuação:** (-2) + (-1) = **-3**

**Ramo 3: Inserir Gap na Seq. 1 ({red}(-) vs {red}(G))**
* **Cálculo:** `Custo(Gap -,G)` + `Pontuação("AG" vs "A")`
* **Pontuação:** (-2) + (-1) = **-3**

O algoritmo escolheria o **Ramo 1**, pois **+2** é a pontuação máxima entre (+2, -3, -3).
:::
???



Ele repete esse processo recursivamente para os subproblemas até que ambas as sequências acabem.
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

## Da Recursão à Programação Dinâmica
A recursão é ineficiente porque recalcula os mesmos subproblemas **várias vezes**.

E se pudéssemos armazenar o resultado de um subproblema (como Score("AT", "A")) na primeira vez que o calculamos? Da próxima vez que precisarmos dele, em vez de recalcular, nós simplesmente **consultaríamos o valor salvo**.

E isso é a base da **programação dinâmica**, resolver um problema recursivo, mas **"memorizando" (cacheando)** as soluções dos subproblemas. Mas isso leva à questão: como armazenar essas soluções?

??? Atividade: Mapeando os Subproblemas
Nossa função recursiva `score(S1, S2)` é chamada com **vários sufixos (trechos)** das sequências originais. Olhando isso de outra forma, a função é chamada com **todos os prefixos das sequências**:

- Prefixos de "ATC" (Seq 1):
```
"", "A", "AT", "ATC" 
```
(4 possibilidades)

- Prefixos de "AG" (Seq 2): 
```
"", "A", "AG" 
```
(3 possibilidades)

**Qualquer subproblema** que precisamos calcular é uma **combinação** de um prefixo da primeira sequência com outro da segunda.

Ou seja, precisamos da estrutura de dados em que podemos armazenar **um valor para cada combinação** possível de prefixos. **Qual estrutura é essa**?

::: Dica
Lembre-se de uma tabela de duas dimensões
:::

::: Gabarito
A estrutura mais natural para organizar dados baseados em duas "chaves" (índice da Seq 1 e índice da Seq 2) é uma **matriz** (mais especificamente uma **matriz de cache** nesse caso)

- As linhas da matriz podem representar os prefixos de "ATC".

- As colunas da matriz podem representar os prefixos de "AG"

Vamos criar uma matriz `M` onde cada célula `M[i][j]` vai armazenar a pontuação máxima do alinhamento entre:
- Os primeiros i caracteres da Sequência 1

- Os primeiros j caracteres da Sequência 2
???

Com isso definido, responda:

??? Exercício
Considere as seguintes sequências:
- Sequência 1: ATC
- Sequência 2: AG

Quais as dimensões da estrutura de dados utilizada para armazenar as pontuações?

::: Dica
Lembre-se que o prefixo vazio é uma possibilidade
:::

::: Gabarito
Precisariamos de uma matriz (3 + 1) linhas por (2 + 1) colunas. O +1 é considerando o caso do prefixo vazio.
:::
???

## A matriz de pontuação: casos base
Como estabelecido anteriormente, matriz vai armazenar a pontuação de cada **combinação de prefixos**. Para preenchê-la, vamos começar com os casos triviais: os casos base da recursão.

**Caso Base 1: ScoreMatrix[0][0]**

Significado: Qual o score de alinhar **""** (prefixo de tamanho 0) com **""** (prefixo de tamanho 0)?

Resposta: 0.

**Caso Base 2: A Primeira Linha (ScoreMatrix[0][j])**

Significado: Qual o score de alinhar **""** com **"A"**, ou **""** com **"AG"**?

Resposta: Para alinhar "AG" (prefixo de tamanho 2) com uma string vazia, o único alinhamento possível é:

```
--
AG
```

Cálculo: Isso são 2 gaps. Se a penalidade de gap é -2, os scores da primeira linha serão: 0, -2, -4, ...

Lógica: `ScoreMatrix[0][j] = ScoreMatrix[0][j-1] + custo_gap`

**Caso Base 3: A Primeira Coluna (ScoreMatrix[i][0])**

Significado: Qual o score de alinhar **"A"** ou **"AT"** com **""**?

Resposta: O mesmo raciocínio. Para alinhar "AT":

```
AT
--
```

Cálculo: Isso também são 2 gaps. Os scores da primeira coluna serão: 0, -2, -4, ...

Lógica: `ScoreMatrix[i][0] = ScoreMatrix[i-1][0] + custo_gap`

Veja como a inicialização da matriz (que antes parecia uma "regra" arbitrária) é, na verdade, apenas a solução lógica para os casos base da recursão.

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


??? Reflexão 1

Qual o motivo de existirem essas três setas que vem da diagonal, da esquerda e do topo, como mostrado na ultima figura para o cálculo da pontuação?


::: Dica
São três possibilidades, três pontuações, e cada recursão tinha três ramos...
:::

::: Gabarito
O motivo para fazermos isso é o fato de estarmos buscando pela maior pontuação dentro de cada comparação entre gap vs letra, letra vs letra, letra vs gap. Assim, já ganhamos uma intuição do porque esse modelo é mais eficiente.
::: 

???


??? Exemplo
A sequência de imagens a seguir representa o preenchimento da matriz das sequências **ATCGAGC** e **ATGAC** utilizando o algoritmo de Needleman-Wunsch com as seguintes pontuações:

- Match: +1
- Mismatch: -1
- Gap: -2

:pontuacoes
???
