---
layout: post
title: Automatizando o fluxo do git
published: true
date: 2014-10-31
tags: gitflow git
---

# The Flow
Git é uma ótima ferramenta de source control, bem flexível permitindo que cada empresa/pessoa/projeto trabalhe de uma maneira diferente com um workflow específico.
E como foi com os design patterns, quanto mais avançamos na utilização do git (ou qualquer outro source control) iremos nos deparar com alguns problemas e soluções. Estas soluções em alguns casos são bem parecidas e podem acabar virando um padrão/recomendação para utilização.

Com o git não foi diferente, podemos por exemplo trabalhar usando um [Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) ou [Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).

Neste artigo pretendo abordar o Gitflow Workflow que basicamente é uma extenssão do Feature Branch Workflow

Resumidamente o Gitflow Workflow consiste em:

1. Branch master: Produção
2. Branch releasexx: Preparação para a próxima versão
3. Branch develop: Em Desenvolvimento
4. Branch featurexxx: Em Desenvolvimento a feature x
5. Branch hotfixxxx: Em Desenvolvimento o hotfix x
6. Tag vX.X.X.X : Versão X

O processo consiste em desenvolver as features em branchs específicos, um para cada feature, e então realizar um merge com o branch develop, posteriormente com o branch release.
Caso necessário necessário, hotfixes podem ser aplicados diretamente ao branch release para uma correção urgente.

![gitflow](https://www.atlassian.com/git/images/tutorials/collaborating/comparing-workflows/gitflow-workflow/05.svg)

Isto é bastante comum, e faz todo sentido pois nos permite identificar e migrar de um ponto do código para outro "rapidamente" quando necessário, trabalhar com grandes equipes, além de manter uma rastreabilidade do código.
Caso tenha interesse em aprofundar neste flow, todo este processo está descrito [neste artigo](http://nvie.com/posts/a-successful-git-branching-model/), o objetivo aqui é demonstrar o [gitflow](https://github.com/nvie/gitflow) e como ele pode nos ajudar a seguir fluxo de forma rápida e intuitiva.

# O problema
O processo em sí não é o problema, e sim a quatidade de comandos que você tem de digitar ao passar por cada passo:

1. Inicia uma feature: `git checkout -b feature/xxx`
2. Work on feature .... `git commit -a -m "My work"`
3. Finaliza uma feture: `git checkout develop`, `git pull develop`, `git merge feature/xxx`
4. Hotfixes ...
5. Releases ...

Sempre que aderimos a um processo, temos de pesar todos as vantagens e desvantagens, e procurar meios de automatizar para que não corramos o risco de não seguir ou utilizar de forma errada este processo.

Neste caso, precisamos automatizar a quantidade de código necessária para seguir este fluxo, além de tornar mais legível os passos necessários.

# A solução
[Gitflow](https://github.com/nvie/gitflow) é uma coleção de git extensions que permite abstrair este fluxo, tornado-o mais interativo e expressivo.
Com ele podemos seguir este workflow de uma forma bem prática e intuitiva.


# Instalação (Windows)
Apesar de muito bem [descrito na página do produto](https://github.com/nvie/gitflow/wiki/Installation), a instalação no Windows pode ser um pouco confuso, então resolvi compartilhar como fiz a instalação em minha máquina:

1. Clone o repositório: `git clone --recursive git://github.com/nvie/gitflow.git`
2. Navege para a pasta: `cd gitflow\contrib\`
3. Execute o arquivo: `msysgit-install.cmd` **Em modo admin**

É importante que o [Cygwin](http://www.cygwin.com/), [msysgit](http://code.google.com/p/msysgit/), [util-linux](http://en.wikipedia.org/wiki/Util-linux) e o wget estejam devidamente instalados no seu pc;

Após realizar estes passos, no seu prompt de comando ao digitar `git flow` você deverá ver:

{% highlight Bash %}
git flow
usage: git flow <subcommand>

Available subcommands are:
   init      Initialize a new git repo with support for the branching model.
   feature   Manage your feature branches.
   release   Manage your release branches.
   hotfix    Manage your hotfix branches.
   support   Manage your support branches.
   version   Shows version information.

Try 'git flow <subcommand> help' for details.
{% endhighlight %}

# Get started
Agora que já instalamos gitflow, precisamos inicializá-lo usando o comando `git flow init` no seu repositório. Este comando irá configurar os branchs necessários para o workflow.

## Feature Branches
Da mesma forma que no Feature Branch Workflow, o gitflow possui branches para cada feature.

{% highlight Bash %}
git flow feature [list] # Lista as features
git flow feature start <name> # Inicia o branch para a feature específicada
git flow feature finish [<name|nameprefix>] # Finaliza o branch para a feature específicada
git flow feature publish <name> # Publica a feature no repositório central
{% endhighlight %}

## Release Branches
Com a feature finalizada, podemos preparar a versão para produção. Release branches tem este objetivo.
É nele por exemplo que você modifica versão nos arquivos, adiciona release notes, realiza testes.

{% highlight Bash %}
git flow release start <version> # Inicia a versão
git flow release finish <version> # Finaliza a versão
git flow release publish <name> # Publica a versão no repositório central
{% endhighlight %}

## Hotfixes Branches
Ao lançar uma versão eventuais bugs podem aparecer, esta branch é usada para correções de bugs em produção.
Ao finalizar, o hotfix é aplicado ao branch `master` e `develop`.

{% highlight Bash %}
git flow hotfix start <version>
git flow hotfix finish <version>
git flow hotfix publish <version>
{% endhighlight %}

## Support Branches
Segundo a documentação são recursos esperimentais, onde o time desenvolve algo mas não quer aplicar a próxima release.


## Usage
Sua utilização é bem simples, para trabalhar em uma nova feature use o comando `git flow feature start <your feature>`, ao finalizar esta feature `git flow feature finish <your feature>`.

O gitflow irá executar todos os comandos necessários para o workflow merge, branch, checkout etc.

Você pode publicar esta feature no repositório origin usando o comando: `git flow feature publish <your feature>` ou realizar um pull `git flow feature pull <remote> <your feature>`

Este mesmo conceito segue para release e hotfixes.

# Projetos existentes
Se você deseja utilizar o gitflow em um projeto já em andamento, não existem problemas, somente algumas recumendações.

 1. Elimine todos os branches, exceto o master antes de iniciar o gitflow.
 2. Oriente a equipe a sempre usar `git flow` nos comandos git.

Após isso é só executar `git flow init`.

# Conclusão
Eu realmente estou muito satisfeito com o gitflow, para se ter uma ideia de como ele automatiza este workflow para nós, um simples comando como: `git flow release finish -F -p iteration01` é traduzido para:

{% highlight Bash %}
git checkout master
git fetch origin master
git merge –no-ff iteration01
git tag -a iteration01
git push origin master
git checkout develop
git fetch origin develop
git merge –no-ff iteration01
git push origin develop
git branch –d iteration01
{% endhighlight %}

Imagine ter de digitar todos estes comandos sempre que finalizar uma release.
{: .notice}

# References
 * [A short introduction to Git Flow](https://vimeo.com/16018419)
 * [Using git-flow to automate your git branching workflow](http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/)
 * [Installing Git-flow in Windows](http://xinyustudio.wordpress.com/2012/03/26/installing-git-flow-in-windows/)
 * [GitFlowGuide](https://github.com/oakwood/GitFlowGuide#initializing-git-flow-on-an-existing-repository)
 * [A Step by Step Guide to using GitFlow with TeamCity – Part 3 – GitFlow Commands](http://blogs.endjin.com/2013/04/a-step-by-step-guide-to-using-gitflow-with-teamcity-part-3-gitflow-commands/)
