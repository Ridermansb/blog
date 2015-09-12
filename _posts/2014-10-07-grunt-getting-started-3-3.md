---
layout: post
title: Grunt - Getting Started 3-3
published: true
date: 2014-10-07
tags: grunt cmd generators
---

Este é o terceiro post da série sobre [Grunt](http://gruntjs.com/)

* [Getting started]({% post_url 2013-10-22-grunt-getting-started-1-3 %})
* [Automação]({% post_url 2014-10-07-grunt-getting-started-2-3 %})
* [Customizações]({% post_url 2014-10-07-grunt-getting-started-3-3 %})3 %})

-----------------

# Tarefas customizadas
Você pode criar sua própria task e definir sua lógica. Exemplo:

    grunt.registerTask('custom', 'My custom task', function() {
      grunt.log.writeln('Seu código rodando aqui...');
    });

A documentação do objeto `grunt` está disponível na [documentação da API](http://gruntjs.com/api/grunt)

Para rodar sua task, utilize a mesma nomenclatura de uma task normal: `grunt custom`.

Você pode inclusive passar targets para sua function:

    grunt.registerTask('custom', 'My custom task', function(arg1, arg2) {
      grunt.log.writeln('Seu código rodando aqui...');
      if (arg1)
        grunt.log.writeln('Arg1: ' + arg1);
     if (arg2)
        grunt.log.writeln('Arg2: ' + arg2);
    });

Para executar: `grunt custom:Argumento1:Arqumento2`

Function nada mais é do que um callback executado logo após a execução da task.
Caso já tenha definido uma task (por exemplo `coffee`) nas configurações, então a task é executada e logo em seguida o callback:

    grunt.registerTask('coffee', 'Log coffee compile.', function() {
      grunt.log.writeln('coffee:' + this.target + ' compiled!');
    });

Desta forma, primeiro `coffee` será executado (compilado os arquivos JS) em seguida o callback.

# Reutilizando código
A medida que o número de tasks cresce, a quantidade de códigos e a complexidade crescem juntos. E claro, consequentemente a [manutenabilidade](http://pt.wikipedia.org/wiki/Manutenabilidade) diminui.

Em Grunt existem alguns recursos que podem ajudar a manter o código mais limpo como [Templates](http://gruntjs.com/configuring-tasks#templates) e [Arquivos de configuração](http://gruntjs.com/configuring-tasks#importing-external-data)

## Templates
Templates é a capacidade do GruntJS expandir configurações das tarefas.
Por exemplo, muitas tasks como a Concat possui uma opção chamada `banner` que adiciona um texto no topo dos arquivos concatenados.

Vamos supor que em nossa empresa seja obrigatório adicionar no topo dos arquivos JS um texto e a data em que o arquivo foi gerado. Com Grunt poderíamos criar uma variável com este texto e utilizá-la em todas as nossas tasks:

    concat: {
        options: {
            banner: '/* <%= copy %> */\n',
          },

    ...

    copy: 'Desenvolvido por Riderman em <%= grunt.template.today("yyyy-mm-dd") %>'

Bem simples, tudo o que temos de fazer é colocar nossa variável entre `<%=` e `%>` e o Grunt fará o resto.
O resultado é: `/* Desenvolvido por Riderman em 2013-04-22 */`

Podemos reutilizar esta variável por exemplo na task `uglify`.

## Arquivos de configuração

Bem parecido com os templates com a única diferença que arquivos de configuração ficam em arquivos separados ao `Gruntfile.js`. Podendo ser `JSON` ou `YAML`.

Aqui na [Bind Solution](http://bindsolution.com.br) utilizamos bastante em nossa rotina de backups para armazenar paths e senhas por exemplo.

Sua utilização é a mesma que os templates, com um detalhe que você deve carrega-lo antes utilizando o comando: `pkg: grunt.file.readJSON('meuarquivo.json')`

A forma de utilização é a mesma: `Olá <%= pkg.nome %>`

# Criação de plugins

Como todo bom framework, Grunt permite sua extensão através de plugins; O processo de criação é bem simples descrito na [página do projeto](http://gruntjs.com/creating-plugins).

Basta utilizar o template [disponível no Github](https://github.com/gruntjs/grunt-init-gruntplugin) e criar seu código.
