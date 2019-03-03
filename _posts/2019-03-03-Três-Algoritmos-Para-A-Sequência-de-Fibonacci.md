
##### Introdução

A sequência de fibonacci é uma velha conhecida dos que já procuraram aprender sobre programação. Eu lembro que foi um dos primeiros algoritmos com que cruzei, na época passado como uma tarefa em C++, quandoe eu nem imaginava que ainda iria me interessar tanto por programação. 

Por isso, esse post não deve soar tão estimulante. De fato pode não ser para alguns mas há aqui algoritmos que me deixaram deslumbrado e eu queria compartilhar. O primeiro não é um deles, é o jeito _standard_. O segundo, o jeito recursivo, não é também tão impressionante, chegando até a ser pior (exceto quando o melhoram com um truquezinho). Mas o terceiro é onde está a beleza do post, o jeito da Álgebra Linear.

Eu vi que se pode calcular a sequência de fibonacci com matrizes na aula 22 do curso de Linear Algebra do MIT Open Course Ware, com o Gilbert Strang, que aliás só tem aulas exemplares. A maneira com que isso se dá é por meio dos autovalores e autovetores de uma matriz, que uma vez entendidos podem ser uma ferramente poderosa para entender o que acontece quando elevamos uma matriz a uma k potência. Mas vejamos os casos mais simples antes.

##### Pela maneira simples

Apenas para recordar o que é a sequência de fibonacci, ela é tal que, dados os dois primeiros números da sequência (comumente 0 e 1), o próximo número é a soma dos dois que o precedem. 

$$F_0 = 0, F_1 = 1$$
$$F_n = F_{n-1} + F_{n-2}$$

Com isso em mente, podemos escrever o algoritmo que já deve ser conhecido por muitos.


```python
def std_fib(n):
    a, b = 0, 1 # tuple assignment
    
    for _ in range(n):
        fib = a + b
        a, b = b, fib
        
    return fib

[std_fib(i) for i in range(1, 13)]
```




    [1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233]



Acredito que não muito possa ser dito sobre este algoritmo, mas é interessante ver o jeito python de escrevê-lo. 

Por exemplo, legal pontuar como atribuí os valores às variáveis. O que tá rolando ali é chamado de tuple assignment. No Python, você pode atribuir o conteúdo de uma lista ou tuple numa única linha, em outra linguagem talvez precisássemos de variáveis temporárias.

Caso alguém nunca tenha visto esse algoritmo, uma breve explicação. Primeiro atribuímos à variável "a" o valor 0, e à "b" o valor 1. Depois, no loop, calculamos o número de fibonacci, que é a soma desses dois. O primeiro, então, é 1. Em seguida, o que fazemos é atribuir à "a" o valor de "b" e à "b" o valor do número de fibonacci, de forma que no próximo loop, a soma será 1 + 1, e no próximo 1 + 2, 2 + 3 etc.

Para comparação, vamos testar a eficiência dele.


```python
%timeit std_fib(10000)
```

    2.46 ms ± 96.5 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
    

##### Por recursão

Como não sou estudante de ciência da computação, esse eu conheci recentemente até e achei maravilhoso. Ele tem a vantagem de ser bem legível, limpo.


```python
def rec_fib(n):
    if n == 0 or n == 1:
        return n
    else:
        return rec_fib(n-1) + rec_fib(n-2)
    
[rec_fib(i) for i in range(13)]
```




    [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144]



Esse algoritmo funciona da seguinte forma: Primeiro, temos um caso base (_base case_), em que o valor é conhecido. 

Neste caso, definimos que os números 0 e 1 da sequência de fibonacci são, respectivamente, 0 e 1. Para todos os outros, calculamos de forma recursiva. 

Por exemplo, \F_3 = F_2 + F_1 = F_1 + F_0 + F_1 = 1 + 0 + 1 = 2.\

Ótimo! E é uma bela amostra da beleza e poder da recursão na computação. Vamos ver quão eficiente ele é.


```python
%timeit rec_fib(20)
```

    4.81 ms ± 625 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
    

Sim, foi esse mesmo o output. Para calcular o vigésimo número ele demorou muito mais do que o algoritmo anterior, que calculou um número muito maior!! Por quê será que isso aconteceu? Bom, se você for mesmo tentar seguir o a função está tentando fazer você pode se perder muito facilmente. Uma imagem ajuda a ilustrar isso.

:::::::::::::::inserir imagem aqui:::::::::::::::::

O que acontece é que a função recursiva acaba requisitando os mesmos números de fibonaccis múltiplas vezes e, para números muito grandes, esse excesso de chamadas desnecessárias prejudica muito a perfomance do algoritmo. Muito mesmo, se você chamar a função com 60 como argumento, talvez não tenha paciência para esperar ela terminar de calcular.

Existe um jeito bem simples de resolver esse problema, que vem da assim chamada programação dinâmica, um conjunto de técnias para otimização de algoritmos. A técnica que aqui será usada é chamada de memoization, e consiste em "reciclar" os valores que já calculamos previamente, para não precisar calculá-los previamente. Isso é exatamente o que precisamos!

Agora, como poderíamos fazer isso? Um jeito bem eficiente é usar um hash table, o que no Python é um set ou um dictionary. A vantagem dessa estrutura de dados é que o tempo de procura independe de quantos dados armazenados você tem ali. Vejamos o código.


```python
def f_rec_fib(n, memo = {0: 0, 1: 1}):
    try: # tente retornar um valor no 'memorizador'
        return memo[n]
    
    except: # se não conseguir, calcule o numero
        fib = f_rec_fib(n-1) + f_rec_fib(n-2)
        memo[n] = fib # guarde-o
        return memo[n]
```


```python
%timeit f_rec_fib(20)
```

    178 ns ± 0.385 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)
    


```python
%timeit f_rec_fib(1000)
```

    183 ns ± 8.12 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
    

Muito mais rápido. É bem impressionante o quanto essa técnica pôde melhorar o código. Uma coisa a se notar, no entanto, é que se você chamar a função com 10000, pelo menos aqui dá "stack overflow"... o que eu não esperava.

***
##### Por Álgebra Linear

https://youtu.be/13r9QY6cmjc?t=2074

Finalmente, matrizes. Comecemos primeiro com a equação básica, $F_n = F_{n-1} + F_{n-2}$. 

Só que, para termos um sistema de equações lineares, precisamos de mais uma equação pelo menos. A escolha mais simples é: $F_{n-1} = F_{n-1} + 0\cdot F_{n-2}$. O que, em linguagem de matrizes, pode ser escrito como:

$$\begin{bmatrix}F_{n} \\ F_{n-1}\end{bmatrix} = \begin{bmatrix}1 & 1\\1 & 0\end{bmatrix} \cdot \begin{bmatrix}F_{n-1} \\ F_{n-2}\end{bmatrix}$$

Uma denominação mais interessante para essa relação seria: $ u_{t+1} = Au_t $. Porque queremos saber, por exemplo, qual o \u_{100}\. Como podemos fazer isso? 

Bom, para começar, tomemos um vetor base \u_0\, que no nosso caso pode ser perfeitamente \\begin{bmatrix} 1 \\ 0 \end{bmatrix}\. Assim, segue diretamente que \u_1 = Au_0\. E, disso, que \u_2 = Au_1 = A \cdot A \cdot u_0 = A^2u_0\. Logo, a fórmula geral é: $$u^k = A^ku_0$$.

O que sugere de imediato que a resposta reside nas potências da matriz. Mas isso pode ser bem complicado. Felizmente, existe uma maneira muito conveniente para esses casos, que envolve os autovalores/autovetores (_eigenvalues/eigenvectors_) de uma matriz. Mas, afinal, o que são eles?

Simply put, os autovetores são vetores característicos de uma matriz que, quando transformados por ela, não mudam de direção, mas é possível que mudem em magnitude dependendo do autovalor. Matematicamente, $$Ax = \lambda x$$

Em que \x\ é um autovetor da matriz \A\ e \\lambda\ um autovalor do autovetor. Note que, por isso, o autovetor pode encolher (se \\lambda = 0.5\), ou aumentar (se \\lambda > 1\), ou apontar para a direção oposta (se \\lambda = -1\) quando transformado.

Agora, o próximo passo, um grande passo, é que nós podemos decompor a matriz A em seus autovetores e autovalores. Isto é possível se os autovetores forem todos linearmente independentes. 

Daí que, tratando \S\ como uma matriz dos autovetores e \\Lambda\ como uma matriz de autovalores na diagonal, podemos afirmar que $$AS = S\Lambda$$ O que não é óbvio, mas que pode ser visto deste modo:

$$AS = A \cdot \begin{bmatrix}x_1 & x_2 \\x_1 & x_2\end{bmatrix} = \begin{bmatrix}\lambda_{1} x_1 & \lambda_{2} x_2 \\ \lambda_{1} x_1 & \lambda_{2} x_2\end{bmatrix} = \begin{bmatrix}x_1 & x_2 \\x_1 & x_2\end{bmatrix} \cdot \begin{bmatrix} \lambda_{1} & 0 \\ 0 & \lambda_{2}\end{bmatrix} = S \Lambda$$

Se não ficou claro, cada coluna de S é um autovetor. Quando fazemos \AS\, obtemos uma matriz cujas colunas são os autovetores vezes seus respectivos autovalores (lembre-se de \Ax = \lambda x\). Tal matriz pode ser "diagonalizada" separando esses seus dois componentes como mostrado.

Ok, mas por que isso é útil? Queremos investigar as potências de uma matriz afinal! Essa decomposição de matriz serve justamente para esse propósito. Vejamos.

Vamos multiplicar \AS\ pela direita por \S^{-1}\. Isso dá: \A = S\Lambda S^{-1}\, já que $SS^{-1} = I$. Agora, $A^2 = S\Lambda S^{-1}S\Lambda S^{-1} = S \Lambda^{2}S^{-1}\. E, em geral:

$$A^k = S \Lambda^{k}S^{-1}$$

Retomando lá do início, \u_k = A^ku_0\ pode ser reescrito agora como \u_k = S \Lambda^{k}S^{-1}u_0\. Mas, na verdade, seria mais conveniente agora termos \u_0\ em uma nova "forma", mais especificamente, como uma combinação linear dos autovetores, \Sc = u_0\, onde c é o vetor de coeficientes que resolve esse sistema de equações, \c = S^{-1}u_0\. E, assim, nós temos tudo o que precisamos. 

$$ u_k = A^ku_0 = S \Lambda^{k}S^{-1} Sc = S \Lambda^{k}c $$

Isso implica que, no nosso caso, para uma matrix 2x2 (só dois autovetores), \u_k = c_1\lambda_{1}^{k}x_1 + c_2\lambda_{2}^{k}x_2\. O que indica que a evolução do nosso sistema de equações é ditada pelos autovalores somente. Então, vamos achá-los usando o NumPy.

O primeiro passo é criar a matriz A. Logo depois, obter os autovalores/autovetores, nessa ordem, com a função numpy.linalg.eig().


```python
import numpy as np

A = np.array([[1, 1],
              [1, 0]])

evalues, evectors = np.linalg.eig(A)

print(evalues, evectors, sep = '\n\n')
```

    [ 1.61803399 -0.61803399]
    
    [[ 0.85065081 -0.52573111]
     [ 0.52573111  0.85065081]]
    

Mas, lembrando que queremos os autovalores na matriz diagonal \\Lambda\. Também precisamos calcular o vetor c.


```python
Lambda = np.diag(evalues)

c = np.linalg.solve(evectors, np.array([1, 0])[:, np.newaxis])
```

Agora, vamos olhar a cara da nossa matriz \\Lambda\:


```python
Lambda
```




    array([[ 1.61803399,  0.        ],
           [ 0.        , -0.61803399]])



O primeiro número pode parecer familiar para o leitor, afinal é um dos mais famosos, o número de ouro. E é precisamente ele que descreve como os números de fibonacci estão crescendo!! Relembrando que \u_k = c_1\lambda_{1}^{k}x_1 + c_2\lambda_{2}^{k}x_2\, substituindo temos:

$$u_k = c_1\cdot(1.618)^{k}\cdot x_1 + c_2\cdot(-0.618)^{k}\cdot x_2$$

E fica evidente que, para um k muito alto, o primeiro autovalor é o que determina como os números crescem. E eles crescem bastante rápido, exponencialmente!

Agora, vamos criar uma função para calcular \u_k\. Nela usaremos a matriz \S\ (evectors), o vetor c e a matriz \\Lambda\.


```python
def FIB(k):
    return (evectors @ np.linalg.matrix_power(Lambda, k) @ c)[0]

for i in range(1, 15):
    print(FIB(i), end = '\t')
```

    [1.]    [2.]    [3.]    [5.]    [8.]    [13.]   [21.]   [34.]   [55.]   [89.]   [144.]  [233.]  [377.]  [610.]  

Tudo o que fiz foi calcular \u_k\ com a fórmula e extrair do vetor resultante a primeira linha (\F_k\).

E quanto à sua eficiência?


```python
%timeit FIB(10000)
```

    32.8 µs ± 1.46 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)
    

Foi o mais rápido dos três. O que é compreensível porque estamos usando o NumPy afinal. 

Mas a beleza dessa abordagem está menos em sua eficiência e mais no que a álgebra linear pode nos revelar. A partir dela pudemos entender muito melhor sobre esses números, o que governa o seu crescimento etc. Mas, além disso, problemas com sistemas dinâmicos como esse estão em todos os lugares, e lidar com eles requer inevitavelmente conhecer os autovetores/autovalores de uma matriz e como podemos usá-los.
