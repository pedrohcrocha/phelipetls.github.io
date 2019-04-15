---
title: Puzzle do Campeonato de Soletrar
layout: post
published: false
categories: R Estatística
tags: puzzle probabilidade
comments: true
---

Procurando por puzzles probabilísticos na seção [The Riddler](https://fivethirtyeight.com/tag/the-riddler/) do FiveThirtyEight, encontrei um bastante intrigante, que pedia mais ou menos o seguinte:

> Imagine que você está numa competição de soletrar com outros 8 participantes. 
> Você memorizou 99% do dicionário e cada um dos outros memorizou 1% a menos que você (indo de 98% até 90%).

> Em uma ordem fixa, os competidores soletram uma palavra a cada turno. Se errar, está fora: o último que sobrar vence.
> Qual a probabilidade de você ganhar, dado que estão em ordem crescente de conhecimento (você por último)?
> E a probabilidade em uma ordem decrescente (você primeiro)?

> As palavras são sorteadas aleatoriamente do dicionário. Qualquer palavra não memorizada _será_ incorretamente soletrada.
> Cada turno é independente um do outro.

A intuição nos diz que você é o favorito, o mais provável de ganhar. Mas quão provável? Também deduziríamos que, se você é o
último da fila, essa probabilidade aumenta, porque os outros podem errar antes que você erre. Ao passo que, se você for o primeiro,
aumenta a chance de você se deparar com uma palavra que não conhece antes disso acontecer aos outros.

A maneira mais compreensível de se chegar à resposta é usando a teoria da probabilidade. É fácil perceber que soletrar certo ou errado a palavra
é uma variável aleatória de Bernoulli, com o parâmetro p sendo a porcentagem memorizada do dicionário.

Vamos esclarecer o que estamos precisamente o que queremos calcular, isto é, as condições sob as quais você será o vencedor.

No caso em que você é o último, supondo N palavras sorteadas, você vencerá se tiver acertado N-1 delas, 
tendo os seus concorrentes errado pelo menos uma. Por exemplo, supondo que 10 palavras foram sorteadas, você
só precisa acertar 9, se os outros errarem alguma delas antes de você.

Já no caso em que você é o primeiro, você terá que acertar N palavras, e seus oponentes terão que errar alguma delas logo após.

Então, queremos calcular a probabilidade de você ter acertado N-1 (ou N) palavras consecutivamente E 
de seus oponentes terem errado ao menos uma delas.

###### Distribuição geométrica: a probabilidade de N acertos até primeiro erro

O evento de você acertar x palavras até errar uma é uma variável aleatória discreta, 
com domínio $[0, \infnty]$ e função de probabilidade $P_X = p^x(1-p)$. Esse tipo de evento é melhor modelado pela distribuição geométrica.

Uma v.a. X, de distribuição geométrica com parâmetro p (probabilidade de sucesso), $X ~ \Geom(p)$ 
normalmente expressa o número de fracassos até o primeiro sucesso, em repetidos experimentos de Bernoulli independentes:

$$P_X(X = x) = (1 - p)^xp$$ 

Mas é claro que "sucesso" e "fracasso" são arbitrários, podendo significar qualquer coisa. No nosso caso, o parâmetro da distribuição
é $1-p$, de modo que a função massa de probabilidade seja $P_X(X = x) = p^x(1-p)$, x sucessos consecutivos até o primeiro fracasso.

###### Distribuição binomial: a probabilidade de erro de seus oponentes

Já a probabilidade de seus oponentes errarem ao menos uma das N perguntas é simplesmente a probabilidade complementar deles
terem acertado TODAS as N perguntas, i.e. $p^N$.

Eessa v.a., digamos Y, que pode assumir qualquer valor possível de perguntas sorteadas, $\Im(Y) = [1, \infnty]$, tem distribuição
binomial com parâmetro $N$ e $p$.

Para relembrar, a distribuição binomial modela o número de ocorrências y sucessos em N experimentos de Bernoulli independentes, $Y ~ Binom(N, p)$. 
É esse o nosso caso, sendo que x é também N: o número de N acertos em N palavras perguntadas! 

A função massa de probabilidade, com nossos parâmetros, é dada por:

$$ P_Y(Y = y) = {N}\choose{N}p^{N}(1-p)^{N-N} = 1\cdotp^N\cdot(1-p)^0 = \p^N $$

###### Probabilidade de você vencer em N perguntas

Agora, por fim, a probabilidade de você ganhar o campeonato é dada pelo evento em que você 

