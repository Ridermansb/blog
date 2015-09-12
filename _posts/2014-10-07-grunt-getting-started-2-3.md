---
layout: post
title: Grunt - Getting Started 2-3
published: true
date: 2014-10-07
tags: grunt cmd generators
---

Este é o segundo post da série sobre [Grunt](http://gruntjs.com/)

* [Getting started]({% post_url 2013-10-22-grunt-getting-started-1-3 %})
* [Automação]({% post_url 2014-10-07-grunt-getting-started-2-3 %})
* [Customizações]({% post_url 2014-10-07-grunt-getting-started-3-3 %})

Todo o código utilizado encontra-se no [Github](https://github.com/Ridermansb/grunt-started)
{: .notice}

-----------------

Até o momento aprendemos o que é Grunt, sobre alguns plugins e como configurar tarefas simples. Neste post iremos aprofundar um pouco mais nas tasks para entender melhor sobre o porque das coisas. :)

# Tasks

## Configurações

Cada task é na verdade um comando a ser executado, e no Grunt podemos definir [configurações para cada comando](http://gruntjs.com/configuring-tasks#task-configuration-and-targets).
Por exemplo, no post anterior quando definimos a task coffee temos uma configuração chamada compile:

{% highlight Javascript %}
...
coffee:{
    compile: {
        files: {
            "main.js": "main.coffee"
        }
    }
},
...
{% endhighlight %}

Quando rodamos o comando `grunt coffee` o resultado é este: **Repare na configuração.**

![Compile coffee][1]

Podemos inclusive definir mais do que uma configuração para algumas tasks (como mostrado abaixo), caso você não especifique nenhuma configuração o Grunt irá rodar todas.

{% highlight Javascript %}
...
coffee:{
    compile: {
        files: {
            "main.js": "main.coffee"
        }
    },
    compileJoin: {
        files: {
            "all.js": "\*.coffee"
        }
    }
},
...
{% endhighlight %}

E o resultado do comando `grunt coffee`:

![Grant roda todas as tasks][2]

Se desejar rodar uma task sobre uma configuração específica: `grunt coffee:compileJoin`.

## Opções

Toda task no Grunt possui um atributo chamado `options`, que serve para definir algumas opções de configuração na execução da task. Por exemplo, no plugin [grunt-contrib-coffee](https://github.com/gruntjs/grunt-contrib-coffee) temos a opção [bare](https://github.com/gruntjs/grunt-contrib-coffee#bare) *nu* que serve para gerar o `.js` nu (sem o invólucro de segurança de nível superior função.)

**Exemplo:**

{% highlight Javascript %}
...
coffee:{
    options: {
        bare: true
    },
    compile: {
        files: {
            "main.js": "main.coffee"
        }
    },
...
{% endhighlight %}

Desta forma, os arquivos `.js` gerados não terão uma função wrapper envolvendo todo o javascript. Isto se aplica a todas as configurações.

`Options` também se aplica às configurações:

{% highlight Javascript %}
...
coffee:{
    options: {
        bare: true
    },
    compile: {
        files: {
            "main.js": "main.coffee"
        }
    },
    compileJoin: {
        options: {
            bare: false
        },
        files: {
            "all.js": "\*.coffee"
        }
    }
},
...
{% endhighlight %}

Desta forma as opções definidas em `compileJoin` irão prevalecer, sobrescrevendo as opções do nível superior.

## Files
É importante entender que Grunt foi feito para realizar operações em [arquivos](http://gruntjs.com/configuring-tasks#files), por este motivo temos algumas abstrações como as propriedades `src` e `dest`. Por exemplo, nosso `compileJoin` pode ser escrito da seguinte forma:

{% highlight Javascript %}
...
    compileJoin: {
        options: {
            bare: false
        },
        src: "*.coffee"
        ,dest: "all.js"
    }
},
...
{% endhighlight %}

Ou:

{% highlight Javascript %}
files: [
    {src: ['main.coffee', 'js/*.coffee'], dest: 'all.js'},
    {src: ['src/aa1.coffee', 'src/aaa1.coffee'], dest: 'dest/a1.js'},
],
{% endhighlight %}

Existem também várias [opções de filtros](http://gruntjs.com/configuring-tasks#custom-filter-function). Mas não entraremos em filtros neste momento.

# Refatorando

Agora que entendemos como funcionam as tasks vamos fazer algo divertido :).
No decorrer do desenvolvimento, temos pelo menos dois ambientes: O de dev e o de dist "distribuição" (aquela versão que vai para o ar).

É comum que no ambiente de dev tenhamos de executar uma série de tasks, e o mesmo acontece quando vamos publicar uma versão (dist).

Na versão de dev, como dito no [post anterior]({% post_url 2013-10-22-grunt-getting-started-1-3 %}) , eu tenho de:

 * Compilar meus arquivos sass e coffee
 * Executar testes
 * Otimizar imagens
 * Compilar templates handlebars

e na versão de dist:

 * Minificar js e css
 * Concatenar
 * Lint
 * Compactar arquivos no formato zip
 * Enviar via FTP para o server

*Não vamos fazer todas as tasks acima, mas só algumas pra termos uma noção de como Grunt pode ser útil.*

## Organizando a estrutura

Primeiramente vamos organizar nossa estrutura de arquivos.

![Estrutura de pastas][3]

## Versão dev

### Compilar meus arquivos coffee

Nosso objetivo é compilar nossos arquivos `.coffee` para a mesma pasta de origem (mantendo o nome do arquivo). Para isto vamos utilizar o recurso [Building the files object dynamically](http://gruntjs.com/configuring-tasks#building-the-files-object-dynamically), conforme configuração abaixo:

{% highlight Javascript %}
coffee: {
    dev: {
        expand: true
        ,cwd: "js/"
        ,src: ["**/*.coffee"]
        ,dest: "js/"
        ,ext: ".js"
    }
}
{% endhighlight %}

A propriedade `expand` indica que esta é uma configuração especial. Basicamente estamos dizendo ao Grunt que os arquivos da pasta `js` **proriedade `cwd`** que contém a extensão `.coffee` e em todos os sub-diretórios **proriedade `src`** serão compilados para a extensão `.js` **proriedade `ext`** na mesma pasta de origem **proriedade `dest`**.

*Para este nosso exemplo não temos sub-diretórios na pasta `js`, porém serve como dica :).*

### Otimizar imagens

 1. Primeiramente instale o plugin [grunt-contrib-imagemin](https://github.com/gruntjs/grunt-contrib-imagemin) para otimização de imagens.
 2. Adicione 3 imagens na pasta img.

**Configuração**

{% highlight Javascript %}
imagemin: {
    options: {
        optimizationLevel: 3
    },
    dev: {
        expand: true
        ,cwd: "img/"
        ,src: ["\*\*/\*.jpg"]
        ,dest: "img/"
        ,ext: ".jpg"
    }
}
{% endhighlight %}

## Versão dist

### Minificar js e concatenar

Os plugins para minificar ([grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify)) e concatenar ([grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat)) já estão instalados em nosso projeto, tudo o que temos que fazer é modificar sua configuração para:

{% highlight Javascript %}
uglify: {
    dist: {
        files: {
            'dist/all.js': 'dist/all.js'
        }
    }
},
concat: {
    dist: {
        src: 'js/\*.js',
        dest: 'dist/all.js'
    }
},
{% endhighlight %}

# Alias

Observe que primeiro devemos concatenar e só então minificar o arquivo js. Então vamos aprender sobre [Alias](http://gruntjs.com/creating-tasks#alias-tasks) e como executar várias tasks em uma determinada ordem.

*Alias* nada mais é do que uma lista de tarefas que devem ser executadas em uma determinada ordem. Por exemplo, ao executarmos `grunt` sem informar nenhuma task veja o que ocorre:

![Default task grunt][4]

Observe que grunt tenta rodar uma task `default` que não existe. Então podemos criar um `Alias` chamado `default` e listar as tasks que desejamos que rode caso nenhuma task seja definda:

{% highlight Javascript %}
grunt.registerTask('default', ['coffee:dev', 'imagemin:dev'])
{% endhighlight %}

Desta forma, as duas tasks serão executadas nesta ordem caso nenhuma outra seja informada:

![Rodando task default caso nenhuma task seja definida][5]

Seguindo esta linha, podemos criar um *alias* para rodar as tasks relacionadas a dist:

{% highlight Javascript %}
grunt.registerTask('dist', ['concat:dist', 'uglify:dist'])
{% endhighlight %}

e então rodar o grunt `grunt dist`

![Rodando task dist (alias) no grunt][6]

**No próximo post vamos aprender como criar e customizar estas tasks :)**

Todo o código utilizado encontra-se no [Github](https://github.com/Ridermansb/grunt-started)

# Referências:

 * [creating-tasks](http://gruntjs.com/creating-tasks)

  [1]: http://i.snag.gy/sZtWg.jpg
  [2]: http://i.snag.gy/cPk6S.jpg
  [3]: http://i.snag.gy/Z1utn.jpg
  [4]: http://i.snag.gy/tuXms.jpg
  [5]: http://i.snag.gy/OgAid.jpg
  [6]: http://i.snag.gy/ARNuR.jpg
