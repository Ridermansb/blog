---
layout: post
title: Grunt - Getting Started 1-3
published: true
date: 2013-10-22
tags: grunt cmd generators
---

Este é o primeiro post de uma series com 3 post sobre [Grunt](http://gruntjs.com/).

* [Getting started]({% post_url 2013-10-22-grunt-getting-started-1-3 %})
* [Automação]({% post_url 2014-10-07-grunt-getting-started-2-3 %})
* [Customizações]({% post_url 2014-10-07-grunt-getting-started-3-3 %})

Todo o código utilizado encontra-se no [Github](https://github.com/Ridermansb/grunt-started)

----------

Em uma simples frase, **[Grunt](http://gruntjs.com/) é um automatizador de tarefas!** Seu objetivo é automatizar tarefas repetitivas, por exemplo: Compactar arquivos, concatenar, minificar, executar testes e centenas de outras tarefas, tudo isso de uma forma altamente flexível!

# Problema

São diversos problemas que esta ferramenta se propõe a solucionar, tudo depende do que você está precisando.

Eu (como desenvolvedor web) tenho de:
 * Compilar meus arquivos sass e coffee
 * Executar testes
 * Otimizar imagens
 * Compilar templates handlebars

Para publicar um website:
 * Minificar js e css
 * Concatenar
 * Lint
 * Compactar arquivos no formato zip
 * Enviar via FTP para o server

Percebe como são repetitivas estas tarefas? Com Grunt o podemos automatizar tudo isso.

# Primeiros passos

Grunt roda como um modulo [Nodejs](http://nodejs.org/), sua instalação é bem simples: `npm install -g grunt-cli`, na página [Getting started](http://gruntjs.com/getting-started) você encontra mais detalhes sobre a instalação.

Não vou entrar em detalhes sobre o que é Nodejs ou como instalá-lo (sugiro instalação via [Chocolatey](http://blog.ridermansb.me/post/47151976057/apt-get-para-windows-conheca-o-chocolatey) `cinst nodejs.install`) pois não é o objetivo deste post.

## Preparando o ambiente
Tipicamente Grunt requer dois arquivos `package.json` responsável por descrever o projeto e `Gruntfile.js` onde você irá definir suas tasks e carregar os plugins.

**package.json**

{% highlight Json %}
{
  "author": "Riderman de Sousa Barbosa",
  "name": "grunt-started",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "~0.4.0"
  }
}
{% endhighlight %}


A propriedade mais importante é a `devDependencies` que contém todas as dependências do seu projeto. Repare que nossa primeira dependência é o próprio Grunt. Isto porque `grunt-cli` serve apenas para rodar a versão ao lado do `Gruntfile.js`.

&gt; Til `~` indica que deve ser maior ou igual a versão indicada e menor do que a próxima grande versão. Mais detalhes [neste link](https://npmjs.org/doc/json.html#Tilde-Version-Ranges)

**Instalando o Grunt em nosso projeto**

O próximo passo é pedir ao npm para instalar todas as dependências descritas em nosso `package.json` (em nosso caso apenas o grunt);

{% highlight Bash %}
npm install
{% endhighlight %}

## Primeira task (coffee)

Apenas o grunt não nos ajuda em nada, para fazer algo mais real precisamos de plugins. Por exemplo, se quisermos compilar nossos arquivos `.coffee` para `.js` precisamos de um plugin para realizar esta tarefa.

Existem diversos plugins disponíveis, desde cópia de arquivos até mesmo enviar arquivos por FTP. O time do Grunt mantém o [grunt-contrib](https://github.com/gruntjs/grunt-contrib), uma coleção de 26 plugins (até a data de publicação deste post), cada um responsável por uma tarefa. Além destes, na [página do projeto](http://gruntjs.com/plugins) você pode encontrar dezenas (ou centenas) de outros plugins.  E se não achar o que deseja, você pode inclusive [criar sua própria task](http://gruntjs.com/creating-tasks) (mais detalhes no último post da série).

Para esta primeira task vamos compilar nossos arquivos `.coffee` para `.js`.

**Instalando o plugin**

Primeiramente precisamos instalar o [plugin](https://github.com/gruntjs/grunt-contrib-coffee):

{% highlight Bash %}
npm install grunt-contrib-coffee --save-dev
{% endhighlight %}


`--save-dev` adiciona este plugin como dependência no nosso arquivo `package.json`. Após executado o arquivo terá uma linha extra:

{% highlight Json %}
"devDependencies": {
  "grunt": "~0.4.0",
  "grunt-contrib": "~0.6.1"  &lt;-- Linha extra
}
{% endhighlight %}


**Configurando**
Como dito anteriormente, grunt requer dois arquivos `package.json` e o `Gruntfile.js`. Então crie um novo arquivo com o nome `Gruntfile.js` e o seguinte conteúdo:

{% highlight JavaScript %}
module.exports = function(grunt) {
  // Configurações
  grunt.initConfig({
    coffee:{
        compile: {
            files: {
                "main.js": "main.coffee"
            }
        }
    }
  });

  // Plugins
  grunt.loadNpmTasks('grunt-contrib-coffee');
};
{% endhighlight %}

O arquivo nada mais é do que uma configuração das tasks.
Como configurar depende de cada plugin, neste caso a documentação deste plugin encontra-se [neste link](https://github.com/gruntjs/grunt-contrib-coffee)

**main.js**

Como ainda não temos o arquivo, vamos criá-lo:

{% highlight JavaScript %}
calc = (a, b) -&gt; a + b
result = calc 1, 2
console.log result
{% endhighlight %}


_Simples, e não faz nada útil. Mas para teste é suficiente! :)_

**Testando**

{% highlight JavaScript %}
grunt coffee
{% endhighlight %}

E um novo arquivo `main.js` é criado.

## Segunda task (watch)

Para esta segunda task, vamos vigiar este arquivo, caso sofra alteração grunt deverá compilar novamente para `.js`.
Para realizar esta tarefa precisaremos do plugin [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch).

{% highlight Javascript %}
npm install grunt-contrib-watch --save-dev
{% endhighlight %}

Novamente uma nova linha é adicionada em nosso `package.json`:

{% highlight JavaScript %}
...
  "devDependencies": {
    ...
    "grunt-contrib-watch": "~0.3.1" &lt;-- Linha adicionada
    .
{% endhighlight %}
..


Da mesma forma, precisaremos carregar a task para o nodejs. Altere o arquivo `Gruntfile.js`:

{% highlight Javascript %}
module.exports = function(grunt) {
  // Configurações
  grunt.initConfig({
    coffee:{
        compile: {
            files: {
                "main.js": "main.coffee"
            }
        }
    },
    watch: { // &lt;---- Nova configuração ----
        files: 'main.coffee',
        tasks: ['coffee']
    }

  });

  // Plugins
  grunt.loadNpmTasks('grunt-contrib-coffee');
  grunt.loadNpmTasks('grunt-contrib-watch'); // &lt;-- Carrega a task para o NodeJS ----
};
{% endhighlight %}

Agora é só utilizar:

{% highlight JavaScript %}
grunt watch
{% endhighlight %}

Tente realizar uma modificação qualquer no arquivo `main.coffee` e veja o que acontece.

## Tasks uglify e concat

Agora que já estamos bons, vamos adicionar mais dois plugins, instale o [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify) e o [grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat).
Baixe também o [jquery](http://jquery.com/download/) e adicione-o ao nosso projeto.

Após feita a instalação, modifique seu `Gruntfile.js`

{% highlight JavaScript %}
uglify: {
    my_target: {
      files: {
        'main.min.js': ['main.js']
      }
    }
  }
concat: {
        basic: {
            src: ['jquery-1.9.1.js', 'main.js'],
            dest: 'all.js'
        }
    }
    ...
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-uglify');
{% endhighlight %}


Então é só rodar: `grunt uglify` ou `grunt concat`.

Por enquanto é só, se desejar todo o código utilizado neste post encontra-se [neste link](https://friendco.de/ui#!user/ridermansb/grunt-started).
No próximo post da série veremos como executar várias tasks de uma vez, executar tasks seguindo uma configuração etc.. Ficará bem mais interessante e faremos algo útil :).

# Referências

 1. [Grow Up With Grunt by Ben Alman](http://www.youtube.com/watch?v=Xp6aFno24x4&amp;list=WLE14C9B184C9F1DEC)
 2. [Build Podcast 021 Gruntjs](http://www.youtube.com/watch?v=LoStSbRRA3I)
 3. [Grunt - The Basics](http://www.youtube.com/watch?v=q3Sqljpr-Vc)
 4. [Frequently Asked Questions](https://github.com/gruntjs/grunt/wiki/Frequently-Asked-Questions)
 5. [GruntJs 'grunt' cmd opens Visual Studio?](http://stackoverflow.com/questions/10933111/gruntjs-grunt-cmd-opens-visual-studio)
 6. [A Tutorial for Getting Started with Grunt](http://justinmccandless.com/blog/A+Tutorial+for+Getting+Started+with+Grunt)[](null)
