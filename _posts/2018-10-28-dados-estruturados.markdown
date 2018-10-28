---
layout: post
categories: [seo]
tags: [dados estruturados, otimização, ferramentas]
comments: false
title: "Dados Estruturados"
label: "SEO - Dados Estruturados"
date: 2018-10-27 17:20:00 -0300
---
Recentemente fui encarregado de realizar uma revisão SEO em um produto aqui da empresa, eu estava bem enferrujado pois já haviam se passado uns dois anos dês que estudei sobre o assunto na faculdade, então antes de iniciar essa revisão procurei por publicações sobre, para me atualizar e acabei me surpreendendo com a carência de material em português. Então unindo o útil ao agradável resolvi abordar esse tema em meu primeiro post, algo simples e introdutório na realidade quase pessoal pois irei registrar o que gostaria de ter encontrado quando pesquisei.

Estar bem colocado nas pesquisas tem sido prioridade para grande parte dos negócios online, são muitos desafios que essa tarefa apresenta, particularmente gostaria de abordar um que julgo essencial, que é transpor de forma padronizada para o motor de busca o contexto dos dados de sua página ou aplicação, para isso vou utilizar o padrão [schema.org](https://schema.org) estruturando os dados de um produto fictício que vou usar em um experimento prático.

## Relembrar é viver!

Falando um pouco mais sobre o padrão, é preciso conhecer alguns pontos históricos como que em 2006 onde os principais buscadores,
estabeleceram um padrão para facilitar a indexação de novas páginas, o famoso [sitemap](https://www.sitemaps.org/index.html),
basicamente um arquivo XML com metadados organizados de forma com que um [crawler](https://developer.mozilla.org/en-US/docs/Glossary/Crawler)
possa "compreender" melhor a hierarquia dos links e atualizações da sua página. Esse padrão ficou show de bola e é extremamente
comum tanto em nichos estáticos quanto em aplicações dinâmicas. Mas com o passar do tempo e evolução dos serviços disponíveis
os buscadores passaram a interagir também com o conteúdo da aplicação, a Google já vinha fazendo isso com os `ritch snippets` que através de uma lógica própria
ela consumia o conteúdo da página em questão e exibia um resultado arrojado o que aumentavam o percentual de cliques da página e entregava
uma experiência diferenciada ao usuário, dada a demanda/necessidade foi realizada uma nova convenção entre os buscadores para se obter um padrão
semelhante ao velho de guerra sitemap, foi assim que nasceu, em 2012, [schema.org](https://schema.org/), que é basicamente um
padrão de declaração manifesta e contextual de conteúdo.

## Resultados Ricos?

Agora que conhecemos um pouco sobre essa convenção já podemos pensar em maneiras de utilizar esse padrão e estruturar
as informações que queremos expor, nesse post vamos implementar uma marcação para representar um produto ao motor de buscas do Google,
utilizando algumas recomendações do mesmo por conta da excelente [documentação](https://developers.google.com/search/docs/guides/intro-structured-data),
além da marcação dos dados estruturados ele os utiliza em conjunto com os seus famosos sinais realizados para indexação de páginas e resultado rico ou [resultado avançado](https://developers.google.com/search/docs/guides/search-features), que é exibido em sua SERP de forma que a página obviamente ganha um destaque em relação a outros resultados que não utilizam essa técnica, tomando como base o corpo desse resultado preparei um modelo com algumas informações iniciais de como nosso experimento deve ficar na página de resultados:

![Image of Yaktocat](/assets/img/content/product-rich-result-wireframe.svg)

## Contextualizando

Observando o exemplo acima, podemos notar a presença das informações essenciais a maioria dos anúncios que encontramos por
ai, o mais legal disso é que no código isso é muito declarativo e simples! Tudo usando de propriedades (chave-valor), essas
informações são declarativas e utilizam atributos e tipos de valores já definidos, atualmente são 598 tipos convencionados
pelo schema.org, para sintetizar isso temos a nossa disposição a três formas utilizando Microdata, RDFa, ou JSON-LD, irei utilizar a marcação
com JSON-LD a sintaxe é bem comum e imagino que todos estejam familiarizados com o corpão de um JSON e também por ser relativamente
mais produtivo quando se é implementando em uma SPA, já que essa feature não afetará na marcação de seus componentes, seguindo o
modelo de produto acima nossa marcação fica assim:

{% gist 52e244a871ea63cf6e1ae5fb882e1b87 %}

## A estrutura

Já conseguimos ver que o formato é bem simples e apenas nesse caso usando o método de codificação de dados vinculados [(JSON-LD)](https://json-ld.org),
precisamos do atributo `@context` que informa qual a diretriz vai ser utilizada, como sabem estamos seguindo o padrão do schema.org por
isso temos que deixar explícita logo como primeira chave e só uma vez dentro do objeto.

-  ## TIPOS
Para dizer melhor ao robô do que se trata a página existem os tipos, em sua maioria são abstratos e flexíveis. Tão
flexíveis a ponto de você ter a liberdade de utilizar quantos tipos forem necessários para melhor concretizar o contexto da
página, utilizando tipos existentes e convencionados de acordo com o padrão que estiver utilizando é claro, com o shema.org,
temos uma [lista de tipos](https://schema.org/docs/full.html) disponíveis que estão organizado por hierarquia e abstração,
sendo `Thing` o mais abstrato de todos, mas lembre-se quanto mais concreto melhor, pois o robô identificará com mais
precisão o contexto da página, em nossa experiência utilizei os seguintes tipos [Product](https://schema.org/Product),
[Thing](https://schema.org/Thing), [AggregateRating](https://schema.org/AggregateRating) e [Offer](https://schema.org/Offer),
sendo que o principal tipo na hierarquia de nossa experiência é o `Product` ou seja todos abaixo dele servem para agregar
mais informações e contextualizar nosso produto.

-  ## Atributos
Os atributos servem para concretizar o tipo fornecendo com informações sobre ele, no nosso exemplo podemos identificar
alguns como `name`, `ratingCount`, `image` e `aggregateRating`, alguns são abstratos e podem ser utilizados em vários tipos
diferentes, outros são específicos utilizados apenas em um tipo, quanto mais atributos você utilizar mais concreto se torna
o tipo, para identificar e conhecer melhor os atributos do tipo que você estiver utilizando basta acessar a especificação do
tipo na página dele, acessando no nosso caso o [Product](https://schema.org/Product), você vai encontrar uma tabela com as seguintes
informações: propriedade, valor esperado (primitivo ou composto), sua descrição ou caso de uso.

-  ## primitivo e composto
Bom se você acessou a especificação do tipo `Product` deve ter se perguntado o que são tipos primitivos e compostos, é bem
simples, como qualquer linguagem de programação você tem os tipos triviais, `String`, `Int`, `Double` e etc. Sua declaração
é literal basta declarar o valor como o caso do atributo `name` que espera simplesmente uma `String`. Já como atributo composto
temos o `brand` que espera um tipo que possa contextualizar uma marca como `Organization` ou `Thing`.


## validação

Depois de montarmos o corpo de nosso exemplo, com tipos e seus atributos, chegou a hora de validá-lo, e para isso a Google também
oferece uma ótima [ferramenta](https://search.google.com/structured-data/testing-tool/u/0/), sendo possível obter o preview do
resultado rico na SERP. Infelizmente essa ferramenta ainda não esta 100% e não conseguimos essa visualização para todos os tipos ou em
alguns casos o preview simplesmente ignora alguns atributos, mas a validação de fato cobre todos os tipos convencionados! Com esse
[link](https://goo.gl/kFaoWj) você pode testar iterativamente o nosso exemplo via URL, ou inserindo seus snippets na guia esquerda e
ir acompanhando com o botão validar no centro inferior da ferramenta.

Notando a guia direita temos uma tabela com os dados estruturados da marcação que estamos validando, caso o validador encontre algum
erro ele informa na parte superior em vermelho, isso também inclui avisos que são informações que vão ajudar a deixar sua estrutura
ainda mais rica. Já o preview é acessado atráves do botão visualizar, ele te direciona para uma página com uma simulação da página de
resultados do Google já com a estrutura do resultado rico como primeiro resultado da busca.

## Conclusão
Bom é aqui que vou deixando vocês, tentei não me prender a explicação dos atributos e tipos que utilizei no exemplo mas
sim no conceito e como utilizar/encontrar os outros pois esse assunto é profundo e particularmente acredito que vocês não devem
focar em memorizar nenhum, pois cada contexto exige sua combinação de atributos e atributos por tanto sempre consultem a
[documentação do schema.org](https://schema.org/) e da [Google](https://developers.google.com/search/docs/guides/intro-structured-data).
Do fundo do meu coração eu agradeço o tempo investido lendo esse meu registro, tentarei escrever mais alguns textos sobre SEO
futuramente (Marcação semântica, Sitemap e AMP), e uma última dica é que isso que vimos aqui não é bala de prata,
como diria o fenomenal [Willian Justen](https://willianjusten.com.br), então não pensem que somente estruturar os dados vai deixar
seu site top e sempre na primeira página das pesquisas e que todo robô vai compreender o contexto de sua página, isso é um trabalho
complexo de várias frentes e muitas vezes até incerto.
