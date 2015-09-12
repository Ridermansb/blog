---
layout: post
title: Alguns comandos interessantes do git
tags: git
published: True
---

## Staching

Já passou pela situação de em determinado momento você está trabalhando em algo complexo e uma demanda urgente chega. Então você tem de parar o que está fazendo e trabalhar nesta demanda para só depois continuar o seu trabalho anterior?

A pergunta que surge é, o que fazer com o seu código? Commit pode não ser a solução uma vez que ele ainda não está finalizado.

Criar uma nova pasta para o projeto, executar o `pull` e trabalhar na nova demanda? Talvez sim, mas existe uma solução melhor!

[Stashing](http://git-scm.com/book/en/Git-Tools-Stashing) existe justamente para isto, guarda seu  código em um pilha para que posteriormente você possa recuperá-lo.

{% highlight bash %}
git stash
{% endhighlight %}

Neste ponto o código alterado é arquivado em uma pilha; Você pode trabalhar na demanda urgente.

Após finalizar a demanda utilize o comando `apply` para recuperar o seu código:

{% highlight bash %}
git stash apply
{% endhighlight %}

Este comando irá carregar novamente seus arquivos.
É possível inclusive adicionar várias listas de stash.

{% highlight bash %}
git stash list
{% endhighlight %}

Para aplicar uma lista específica:

{% highlight bash %}
git stash apply stash@{2}
{% endhighlight %}

## File Annotation

Quando nos deparamos com algum bug, já pensou em como recuperar quem inseriu aquele bug no código? Para descobrir teriamos de saber quem alterou determinada linha e quando.

Git tem um [recurso](http://git-scm.com/book/en/Git-Tools-Debugging-with-Git#File-Annotation) chamado `blame` que permite listar quem e quando foi realizado a última alteração em determinada(s) linha(s).

{% highlight bash %}
git blame -L 2,6 myfile.txt
{% endhighlight %}

* `-L` limita as linhas 2 a 6.
* `-C` exibe alterações como no nome do arquivo

## Submodules

[Submodules](http://git-scm.com/book/en/Git-Tools-Submodules#Starting-with-Submodules) são módulos do seu repositório, literalmente sub-repositórios dentro de um repositório master.
Este recurso é muito útil por exemplo quando você tem um projeto de importação/projetos secundários ao projeto principal e deseja manter o código destes projetos em um repositório separado.

Sua utilização é bem simples:

{% highlight bash %}
git submodule add {git-url} {path}
{% endhighlight %}

Este comando irá criar um arquivo `.gitmodules` com o mapeamento de todos os seus sub-repositórios.
A partir dai, você pode efetuar commits/pull/push normalmente o git irá tratar os sub-repositórios de forma separada ao repositório master.

### Referencias

 * [http://www.speirs.org/blog/2009/5/11/understanding-git-submodules.html](Understanding Git Submodules)
 * [https://www.youtube.com/watch?v=sabx_jxdzTQ](adding a git submodule)
