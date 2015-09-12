---
layout: post
title: comparativo-cassette-vs-asp.net-web-optimization
tags: cassette, asp.net-mvc
published: True

---

Sempre fui focado em otimizações web, desde quando comecei a trabalhar com web estudei, li e realizei vários testes sobre como otimizar sites. Fui inclusive [colocado a prova pelo famoso Sérgio Lopes](http://sergiolopes.org/screencast-analise-performance-web-datafilme/) que fez um [screencast sobre análise de performance web](https://www.youtube.com/watch?v=f5ZZEtGCxrQ) em um dos sites que desenvolvi.

Hoje vamos focar em bundling e minifications exclusivamente. Não vamos entrar em detalhes sobre o que é, como utilizar ou  ou os benefícios de bundle e minification pois isto você pode encontrar facilmente na internet. O objetivo deste post é realizar um comparativo entre a famosa ferramenta [Cassette](http://getcassette.net/) (que **até o momento** utilizo em meus projetos) com o ASP.NET Web Optimization da Microsoft [disponível a partir do Asp.net 4.5](http://www.asp.net/vnext/overview/aspnet/whats-new#_Toc318097384).

# O ambiente

Para realizar os teste, vou pressupor que você já tenha um site funcional com alguns arquivos *.js* e *.css* em seu projeto.

## Repositório

Para cada ferramenta vamos criar um [branch](http://mercurial.selenic.com/wiki/Branch) e a que mais se encaixar em nosso projeto, faremos o merge com o branch default.
Em meu projeto utilizo Mercurial, mas fique a vontade para utilizar qualquer outro repositório que mais lhe agrade.

Começaremos com o ASP.NET Web Optimization da Microsoft, então vamos criar um branch para ele:

`hg branch bundle-aspnet`


## Mensurando os testes

Para quem não conhece, [Glimpse](http://getglimpse.com/), é uma ótima ferramenta para realizar vários tipos de testes, inclusive performance. Vamos utilizá-lo em nossos testes.

# Asp.net Bundles

Vamos criar um branch para implantar o Asp.net Bundles. `hg branch bundle-aspnet`.
Instale o pacote `Install-Package Microsoft.AspNet.Web.Optimization`.



# Referências

 * [Bundling and Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
 * [Asp.net MVC 4 Bundling and minification](http://www.davidhayden.me/blog/asp.net-mvc-4-bundling-and-minification)
 * [What's New in ASP.NET 4.5 and Visual Studio 2012](http://www.asp.net/vnext/overview/aspnet/whats-new#_Toc318097384)
 * [ASP.NET BUNDLING AND HOW IT COMPARES WITH CASSETTE](http://codewith.us/asp-net-bundling/)
