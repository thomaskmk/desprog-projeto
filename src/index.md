Algoritmo de Needleman-Wunsch
======

O algoritmo de estudo é um método clássico para o alinhamento global de sequências biológicas.  Ele utiliza programação dinâmica para encontrar a melhor correspondência entre duas sequências, considerando penalidades para gaps e recompensas para correspondências.

Antes de avançarmos para sua definição rigorosa vamos entender o que é uma sequência.

Definição de Sequência
---------------------

Uma sequência é uma série ordenada de elementos, que podem ser letras, números ou símbolos. No contexto da biologia, sequências geralmente se referem a cadeias de nucleotídeos (DNA ou RNA) ou aminoácidos (proteínas). 

O alinhamento de sequências é o processo de dispor duas ou mais sequências, uma sobre a outra, inserindo lacunas (gaps) onde necessário. O objetivo dessa organização é maximizar a quantidade de **caracteres iguais em cada posição**, que visualmente nos permite **identificar regiões de similaridade**. 

Para cada sequência que caracteres, podemos fazer varios alinhamentos possíveis. 
Por exemplo, para as sequências de DNA, **GATTACA** **GATCA**, observe os diferentes alinhamentos possíveis:

: diferentes_alinhamentos

Dessa forma, já ficou mais visível que existem diversas maneiras de alinhar duas sequências.


??? Praticando

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
Cenário considerado ruim e também que subtraímos um valor moderado da pontuação do alinhamento.

- **Gap** (Lacuna): quando um caractere é alinhado com um gap. Exemplo: A vs -
Pior cenário em que subtraímos um valor significativo da pontuação do alinhamento.


Agora, você já é capaz de entender como o algoritmo avalia cada alinhamento possível entre duas sequências. Mas você pode estar se perguntando como o algoritmo escolhe esse sistema de pontuação. Esse ponto é bastante importante, pois pode causar confusão. 


Para que os pontos sejam definidos, é importante esclarecer que são **apenas** parâmetros, ou seja, são apenas valores que podemos ajustar da maneira que quisermos servindo para qualquer caso, com quaisquer valores. No entanto, precisamos ter em mente que:

- {green}(**Matches**) devem ter pontuações {green}(**positivas**).
- {yellow}(**Mismatches**) devem ter as pontuações {yellow}(menos negativas) que gaps.
- {red}(**Gaps**) devem ter pontuações negativas, mas {red}(mais negativas).


Por exemplo, uma configuração comum de pontuação é:
- Match: +2
- Mismatch: -1
- Gap: -3

Então, um exemplo de uma soma seria: 

??? Atividade

Considere as sequências `Seq 1: AGTC` e `Seq 2: ATC`.
Para esta atividade, use o seguinte sistema de pontuação:
* {green}(**Match**): +2
* {yellow}(**Mismatch**): -1
* {red}(**Gap**): -3


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
    * G vs - (Gap): -3
    * T vs T (Match): +2
    * C vs C (Match): +2
    * **Total:** (+2) + (-3) + (+2) + (+2) = **+3**

* **Alinhamento B (```AGTC``` vs ```ATC-```)**
    * A vs A (Match): +2
    * G vs T (Mismatch): -1
    * T vs C (Mismatch): -1
    * C vs - (Gap): -3
    * **Total:** (+2) + (-1) + (-1) + (-3) = **-3**

* **Alinhamento C (```AGTC``` vs ```-ATC```)**
    * A vs - (Gap): -3
    * G vs A (Mismatch): -1
    * T vs T (Match): +2
    * C vs C (Match): +2
    * **Total:** (-3) + (-1) + (+2) + (+2) = **0**
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

* **Alinhamento X:** (`G` vs `G`) + (`A` vs `A`) + (`-` vs `T`)
    * (Match) + (Match) + (Gap)
    * (+2) + (+2) + (-3) = **+1**

* **Alinhamento Y:** (`-` vs `G`) + (`G` vs `A`) + (`A` vs `T`)
    * (Gap) + (Mismatch) + (Mismatch)
    * (-3) + (-1) + (-1) = **-5**

O **Alinhamento X** é o melhor, pois +1 é maior que -5.
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

Nos exercícios anteriores, os alinhamentos foram dados. Mas como poderíamos encontrá-los sozinhos? Para sequências longas, testar todas as combinações é inviável.

A resposta é que não testamos todas. Usamos a **recursão** para *construir* o melhor alinhamento.



O algoritmo funciona "do fim para o começo". Ele se concentra apenas em decidir a última coluna. Para o "resto", ele confia que a recursão já resolveu e encontrou a melhor pontuação para o subproblema. O problema se resume a: "Eu só preciso decidir a última coluna, e a recursão resolve o sub-problema que eu criar."

Vamos praticar essa ideia.

---

??? Atividade

Vamos revisitar um alinhamento da atividade anterior. Assim, considere:

* {green}(**Match**): +2
* {yellow}(**Mismatch**): -1
* {red}(**Gap**): -3

**Alinhamento Completo:**
| Sequência 1 | A | G | T | C |
|:---:|:---:|:---:|:---:|:---:|
| **Sequência 2** | **A** | **-** | **T** | **C** |

**OBS:** Vamos *assumir* que o algoritmo já calculou e nos disse que a pontuação ótima para o "resto" (o subproblema de alinhar `AGT` com `A-T`) é **+1**.

**Resto (Subproblema Resolvido):**


1.  Qual é a pontuação **apenas** da última coluna deste alinhamento?
    ```
    Seq 1:  C
    Seq 2:  C
    ```

::: Dica
Lembre-se que o algoritmo funciona "do fim para o começo". Ele primeiro resolve o "resto" do problema (o subproblema) e depois soma o custo da última ação.
:::

::: Gabarito

1.  **Pontuação da Última Coluna:**
    A última coluna é `C` alinhado com `C`. Isso é um **Match**, que vale **+2**.
:::

???

??? Atividade 

Usando o mesmo conceito de recursão para o exercicio anterior, qual é a pontuação **total** do alinhamento completo referente a ultima atividade ?

:::Gabarito
2.  **Pontuação Total:**
    * Pontuação do Resto (dado): +3
    * Pontuação da Última Coluna (calculado): +2
    * **Total:** (+3) + (+2) = **+5**

!!! Atenção
Observe que **+5** é exatamente o mesmo valor que calculamos "manualmente" para este alinhamento na seção anterior.

Isso confirma a ideia principal: a pontuação de um alinhamento é a soma da pontuação da última ação com a pontuação ótima do subproblema que ela gera, que é justamente o que foi visto durante a disciplina de que uma função recursiva funciona igualmente como um loop.
!!!
:::

???



Ótimo. A atividade anterior nos mostrou *como* a pontuação é somada (`Pontuação Total = Pontuação do Resto + Pontuação da Última Coluna`).

Agora, precisamos responder a pergunta-chave: Como o algoritmo *decide* qual ação tomar? Naquele exercício, a ação (Match) já estava "dado" pelo alinhamento.

Mas o algoritmo, ao começar do zero, não sabe qual é a melhor ação. Ele precisa de uma forma de explorar todas as possibilidades.

??? Atividade

Vamos focar no momento exato em que o algoritmo toma uma decisão.
Considere as duas sequências que estamos analisando:

| Sequência 1 | A | G | T | {red}(C) |
|:---:|:---:|:---:|:---:|:---:|
| **Sequência 2** | **A** | **T** | {red}(**C**) | |

O algoritmo foca **apenas** no final (em vermelho). Pense em como poderíamos construir a **última coluna** do alinhamento.

Quais são todas as ações possíveis que podemos tomar para alinhar esses últimos caracteres?

::: Dica
Pense nas três operações básicas de alinhamento que definimos na seção anterior e que o algoritmo não sabe que existe um gap na sequência 2, então ele vai usar as letras que existem, mesmo que não estejam alinhadas perfeitamente.
:::

::: Gabarito
Existem 3 (e apenas 3) escolhas possíveis para formar a última coluna:

1.  **Alinhar Letra com Letra:** (Neste caso, alinhar o {red}(**C**) da Seq 1 com o {red}(**C**) da Seq 2).
2.  **Alinhar Letra 1 com Gap:** (Alinhar o {red}(**C**) da Seq 1 com um {yellow}(**-**)).
3.  **Alinhar Gap com Letra 2:** (Alinhar um {yellow}(**-**) com o {red}(**C**) da Seq 2).

O algoritmo é forçado a explorar todas as três para ver qual delas leva à melhor pontuação total.
:::

???


Na atividade anterior, vimos que para resolver o problema de alinhar `AGTC` e `ATC`, o algoritmo explora 3 possibilidades (subproblemas):

* **Escolha 1:** Resolver o subproblema `AGT` vs `AT`.
* **Escolha 2:** Resolver o subproblema `AGT` vs `ATC`.
* **Escolha 3:** Resolver o subproblema `AGTC` vs `AT`.


??? Atividade
Agora, vamos focar apenas na **Escolha 1**. O algoritmo agora precisa encontrar a pontuação ótima para o subproblema `AGT` vs `AT`.

Dessa forma, para resolver *este novo subproblema* (alinhar `AGT` e `AT`), quantas novas escolhas (possibilidades) o algoritmo terá que explorar?

::: Dica
A lógica se repete. O algoritmo aplica exatamente o mesmo processo para o subproblema que aplicou ao problema original.
:::

::: Gabarito
A resposta é **3**.

O algoritmo é recursivo. Para resolver *qualquer* problema de alinhamento, ele aplica as mesmas 3 escolhas universais:
1.  Alinhar os últimos caracteres (neste caso, `T` vs `T`).
2.  Alinhar o último da *primeira* sequência(`AGT`) com um gap (`T` vs `-`).
3.  Alinhar um gap com o último de segunda sequência (`-` vs `T`).

Isso significa que o problema original se divide em 3 possibilidades. Cada uma dessas 3 se divide em **outras 3**, totalizando 9 possibilidades após apenas dois níveis.
:::
???

Isso parece uma estrutura muito conhecida por nós. Te lembra ... uma árvore de decisões?
Vamos ver como essa árvore de decisões se comporta quando juntamos todos os conceitos explorados até agora.

??? Exercício
Assumindo que temos as seguintes pontuações:
* Match: +1
* Mismatch: -1
* Gap: -2

Determine a pontuação total para cada uma das três opções (ramos) da recursão ao alinhar as sequências `AG` e `AG`.

::: Gabarito

Para resolver, calculamos a pontuação de cada ramo recursivo, que é o **custo da ação** + **pontuação ótima** do subproblema.

Isso exige que primeiro saibamos as pontuações ótimas dos subproblemas (calculados recursivamente):
* Pontuação(A vs A) = +1 (pois um match é a melhor opção)
* Pontuação(A vs AG) = -1 (o alinhamento ótimo é A- vs AG, com custo `Match(A,A)` + `Gap(-,G)` = +1 + (-2) = -1)
* Pontuação(AG vs A) = -1 (o alinhamento ótimo é `AG` vs `A-`, com custo `Match(A,A)` + `Gap(G,-)` = +1 + (-2) = -1)

Agora, podemos calcular a pontuação de cada ramo do problema principal (`AG` vs `AG`):

**Ramo 1: Alinhar Letra com Letra ({red}(G) vs {red}(G))**
* **Cálculo:** Custo(Match G,G) + Pontuação(A vs A)
* **Pontuação:** (+1) + (+1) = **+2**

**Ramo 2: Inserir Gap na Seq. 2 ({red}(G) vs {red}(-))**
* **Cálculo:** Custo(Gap G,-) + Pontuação(A vs AG)
* **Pontuação:** (-2) + (-1) = **-3**

**Ramo 3: Inserir Gap na Seq. 1 ({red}(-) vs {red}(G))**
* **Cálculo:** Custo(Gap -,G) + Pontuação(AG vs A)
* **Pontuação:** (-2) + (-1) = **-3**

O algoritmo escolheria o **Ramo 1**, pois **+2** é a pontuação máxima entre (+2, -3, -3).
:::
???


O exercício anterior nos mostrou a lógica que o algoritmo usa para decidir em *um único nó* da recursão.

Agora, vamos visualizar o que acontece quando expandimos essa lógica para o problema inteiro. O diagrama abaixo é a **árvore de decisão** para o nosso problema de alinhar `AT` e `AG`, um caso mais simplificado, para que a arvore não fique enorme.

![](arvore_desprog.png)

Vamos analisar a árvore:

1.  O problema "raiz" (AT, AG) se divide nos 3 subproblemas que identificamos (um para cada seta): (A, AG), (AT, A), e (A, A).
2.  Para encontrar a melhor pontuação para (AT, AG), o algoritmo **precisa** resolver todos os três subproblemas.
3.  Mas agora, observe o que acontece:
    * Para resolver o ramo da esquerda, (A, AG), o algoritmo vai se dividir novamente. Uma de suas ações (- vs G) irá gerar o subproblema (A, A).
    * Para resolver o ramo do meio, (AT, A), o algoritmo também vai se dividir. Uma de suas ações (T vs -) também irá gerar o subproblema (A, A).

!!! Atenção
**Subproblemas Sobrepostos**

O que o diagrama prova é que o algoritmo é forçado a calcular a pontuação ótima para o subproblema (A, A) **múltiplas vezes** (uma vez no ramo da direita, e novamente dentro dos ramos da esquerda e do meio).

A recursão pura é extremamente infecificiente: ela não tem memória. Ela recalcula o custo para o *mesmo par de subsequências* várias e várias vezes em galhos diferentes da árvore.

É essa repetição desnecessária, chamada de **subproblemas sobrepostos**, que torna a abordagem recursiva pura tão ineficiente.
!!!

Assim, já concluimos que a eficiencia do algoritmo recursivo é muito baixa. Mas quão baixa exatamente?

??? Atividade

Considerando *m* e *n* os tamanhos das duas sequências a serem alinhadas, determine a **complexidade de tempo do algoritmo recursivo**.

::: Gabarito
No pior caso, a chamada recursiva reduz apenas uma das sequências por vez (ou seja, alinha o caractere de uma com um gap na outra). Nesse caso, até o caso base (ambas as sequências vazias), são ***m* + *n* chamadas**.  

Para cada chamada, existem 3 possibilidades, que geram **3 chamadas recursivas**. Portanto, a complexidade do algoritmo é **$O(3^{m+n})$**.

!!! Atenção
**Subproblemas Sobrepostos**

O algoritmo acaba calculando o score para o *mesmo par de subsequências* múltiplas vezes, em diferentes "galhos" da árvore de recursão.

Para sequências longas, o número de cálculos repetidos cresce exponencialmente, tornando a abordagem recursiva pura completamente impraticável.
!!!
:::

???



Algoritmo de Needleman-Wunsch
---


Na última atividade, vimos que o algoritmo recursivo tenta **todas as possibilidades possíveis**, mas de forma repetitiva. Muitos subproblemas aparecem várias vezes na árvore, e isso o torna ineficiente.

!!! Importante

O algoritmo tenta todas as possibilidades pois o objetivo do alinhamento é **encontrar a melhor forma de encaixar duas sequências**.  
Mas, antes de saber qual é a melhor, o algoritmo precisa **explorar todas as formas possíveis de alinhá-las**, só assim ele tem certeza de que não deixou passar nenhuma opção melhor.

!!!

Agora vamos pensar: será que existe um jeito de **organizar** essas possibilidades, sem perder nenhuma?


??? Atividade:

Como poderíamos organizar os alinhamentos possíveis das sequências `AT` e `AG` em uma matriz?

::: Gabarito
Podemos representar os alinhamentos em uma matriz onde as linhas representam os caracteres da primeira sequência e as colunas representam os caracteres da segunda sequência.


|  | A | T |
|:---:|:---:|:---:|
| **A** | AA | AT | 
| **G** | GA | GT | 


!!! Atenção

Compare com a árvore de recursão que fizemos anteriormente. O que está faltando nessa matriz para que ela represente todos os alinhamentos possíveis?

!!!

:::

???


Para que possamos organizar todos os alinhamentos possíveis, precisamos adicionar **gaps** nas sequências. Assim, a matriz deve incluir linhas e colunas adicionais para representar esses gaps.

??? Atividade: Adicionando os Gaps

Como podemos modificar a matriz anterior para incluir os gaps e representar todos os alinhamentos possíveis entre as sequências `AT` e `AG`?

::: Dica

Observe na árvore recursiva onde os gaps poderiam ser inseridos nas sequências para criar diferentes alinhamentos. Pense em como representar essas possibilidades em uma estrutura tabular.

:::

::: Gabarito
Podemos adicionar uma linha e uma coluna extras para representar os gaps. A matriz modificada ficaria assim:

|   | - | A | T |
|:---:|:---:|:---:|:---:|
| **-** | - - | -A | -T |
| **A** | A- | AA | AT |
| **G** | G- | GA | GT |

:::

???


Agora que entendemos como organizar os alinhamentos possíveis em uma matriz, vamos entender onde a matemática do algoritmo de Needleman-Wunsch entra em cena.

---

**Da Recursão à Tabela**


Na recursão, o algoritmo precisava **decidir a cada passo** o que fazer com o próximo par de caracteres. Ele fazia isso comparando três caminhos possíveis:

1. Alinhar os dois caracteres (match ou mismatch).  
2. Alinhar o caractere da sequência 1 com um gap.  
3. Alinhar o caractere da sequência 2 com um gap.

Cada escolha levava o algoritmo a um **subproblema menor**, que seria resolvido recursivamente.

Agora, observe algo interessante: esses três caminhos são exatamente os **vizinhos** de cada célula na matriz: a diagonal (match ou mismatch), o topo (gap na primeira sequência) e a esquerda (gap na segunda sequência).

!!! Importante
A tabela faz **a mesma coisa que a recursão**, mas de forma organizada.  
Em vez de chamar as funções de novo, ela apenas **olha as pontuações já calculadas** nas células vizinhas. 
!!!


??? Atividade: Ligando a recursão à tabela

Na célula `AA`, quais seriam os três caminhos possíveis para chegar até ela?  
De onde viria cada um deles na matriz?

|   | - | A | T |
|:---:|:---:|:---:|:---:|
| **-** | - - | -A | -T |
| **A** | A- | AA | AT |
| **G** | G- | GA | GT |

::: Dica

Olhe na árvore de recursão e veja quais subproblemas geraram o alinhamento `AA`.
:::

::: Gabarito
Os três caminhos possíveis são:

- **Diagonal:** vindo de `–x–` → comparar `A` com `A` (match).  
- **Cima:** vindo de `–xA` → inserir um gap na segunda sequência.  
- **Esquerda:** vindo de `Ax–` → inserir um gap na primeira sequência.

A pontuação da célula `AA` será a **maior pontuação** entre esses três caminhos,  
somando o custo (match, mismatch ou gap) ao valor do subproblema anterior.
:::

???

---

**Construção da Matriz de Alinhamento**



Ao construir a matriz de alinhamento, o algoritmo armazena as pontuações parciais em cada célula. Cada célula representa a melhor pontuação possível para alinhar as subsequências correspondentes das duas sequências até aquele ponto.

É por isso que, ao calcular cada célula, olhamos para **diagonal, cima e esquerda**, são exatamente os três ramos que a recursão explorava.

Não ficou claro? Vamos praticar.

??? Exercício: Construção da Matriz

Vamos detalhar o processo de construção da matriz considerando as sequências `AT` e `AG`, com as seguintes pontuações:
- Match: +1
- Mismatch: -1
- Gap: -2

::: Dica
Pense em como inicializar a matriz com os valores de gap e como preencher cada célula com base nas pontuações dos vizinhos (diagonal, cima, esquerda). Considere o custo de cada ação (match, mismatch, gap) ao calcular a pontuação para cada célula.
:::


::: Gabarito
:atag
:::
??? 


**Conclusão**


A recursão e a tabela fazem a **mesma sequência de decisões**.  
A diferença é que, na recursão, o algoritmo **refaz os cálculos** toda vez, enquanto na tabela ele **guarda o resultado** de cada subproblema.

Ao preencher a matriz, o algoritmo calcula a melhor pontuação para cada célula com base nas pontuações dos vizinhos (diagonal, cima, esquerda) e o custo associado a cada ação (match, mismatch, gap).

Dessa forma, a matriz é construída de maneira sistemática, permitindo que o algoritmo encontre o alinhamento ótimo ao final do processo.

