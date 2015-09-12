---
layout: post
title: "App-get para Windows? Conheca o Chocolatey!"
date: "2014-04-04 17:03"
tags: cmd chocolatey windows
---

[Chocolatey](http://chocolatey.org/) é uma especie de App-get para Windows.

## Conceito
Para quem não sabe, app-get é um app do Linux que permite que você possa instalar, remover e atualizar aplicativos de forma fácil (por linha de comando) no Linux.

Algo como: `apt-get install dropbox` irá baixar o dropbox e instala-lo em sua máquina.
Ou: `app-get upgrade` irá atualizar todos os apps em sua máquina.

Mais detalhes sobre o app-get [neste link](https://help.ubuntu.com/community/AptGet/Howto)

## Problema
Diferente do Linux, no Windows não temos um app nativo como este, e para instalar algum aplicativo temos de:

 1. Entrar no site do fabricante
 2. Baixar o instalador (.exe ou .msi)
 3. Executar o instalador

O processo complica ainda mais quando queremos atualizar alguma app. Temos de entrar em cada app, procurar pelo comando de atualizar "Check for updates" e atualizar.

Quando pensamos em dezenas (talvez centenas) de apps isso se torna um grande problema instalar e manter todas estas apps.

## Solução
Chocolatey é como um app-get para Windows, permite que você instale, atualize e remova apps via linha de comando.

Algo como: `cinst dropbox` para instalar o Dropbox.
Ou: `cup all` para atualizar todas as apps instaladas.

E sim, [é open-source](https://github.com/chocolatey/chocolatey)!

## Por onde começar?
Primeiro instale o Chocolatey! :). Não vou entrar em detalhes sobre a instalação, como ele funciona como um pacote nuget, a instalação é como em qualquer pacote que já tenha instalado.

Para simplificar recomendo copiar e colar o código de instalação disponível na [página principal do projeto](http://chocolatey.org/) ou [neste link](https://github.com/chocolatey/chocolatey/wiki/Installation) via PowerShell ou CMD.

### Comandos
Após feito a instalação, uma lista de comandos estará disponível no prompt do Windows.

**Buscar**

`chocolatey list packageName` ou `clist packageName` lista as apps disponíveis para instalação. Você pode filtrar, listar versões betas etc.

Exemplo: `clist dropbox` para buscar apps com o nome dropbox

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsList).

**Instalar**  

`chocolatey install packageName` ou `cinst packageName` baixa e instala o app em sua máquina.

Algo bacana no Chocolatey é que você pode criar um arquivo como `Packages.config` contendo **todas as apps** que deseja instalar.

{% highlight XML %}
	<?xml version="1.0" encoding="utf-8"?>
	<packages>
	  <package id="apackage" />
	  <package id="anotherPackage" version="1.1" />
	  <package id="chocolateytestpackage" version="0.1" source="somelocation" />
	</packages>  
{% endhighlight %}

Desta forma, com um único comando `cinst packages.config` todas as apps neste XML serão baixadas e instaladas em sua máquina.

Apps [são pacotes nugets](https://github.com/chocolatey/chocolatey/wiki/CreatePackages#nuspec) hospedados no servidor do Chocolatey.  Se deseja hospedar essas apps em seu PC local ou mesmo em um servidor próprio. O elemento `source` (contendo um diretório local, na rede ou mesmo uma URL) defini qual a fonte desta app.

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsInstall).

**Remover**  

`chocolatey uninstall packageName` ou `cuninst packageName`

Este é bem complexo, vou deixar para outro post. :)

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsUninstall)

**Atualizar**  

`chocolatey update packageName` ou `cup packageName`
Sabe aquele problema de ter de ir ao site do fabricante, verificar se existe uma versão mais nova, comparar com a versão instalada em sua máquina. Pois é, não existe mais.

`cup dropbox` Verifica se existe uma nova versão, se sim baixa e instala automaticamente.  
`cup all` Verifica se existe novas versões em todas as apps instaladas.  
`chocolatey update` Atualiza o Chocolatey para a última versão.

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsUpdate)

**Versão**

`chocolatey version packageName` ou `cver packageName`

Este comando simplesmente compara a versão instalada com a versão disponível no feed.

`cver dropbox` vai retornar a versão instalada e a versão disponível no feed.

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsVersion)

**WebPI**  

[WebPI](http://en.wikipedia.org/wiki/Microsoft_Web_Platform_Installer) ou [Web Plataform Installer](http://www.microsoft.com/web/downloads/platform.aspx) é basicamente um app da Microsoft onde você pode baixar e configurar componentes no Windows.

Então vamos instalar por exemplo o IISExpress:
`chocolatey webpi IISExpress` ou `cwebpi IISExpress` irá baixar (via WebPI) e instalar o IIS Express localmente.

Se desejar ainda pode utilizar o comando `clist`, basta definir o source: `cinst IISExpress -source webpi`

[wiki do comando](https://github.com/chocolatey/chocolatey/wiki/CommandsWebPI)

**ATENÇÃO**
Para que este comando funcione, você deve instalar a versão Command Line do WebPI `cinst webpicommandline`.

### Mais comandos

Chocolatey tem vários outros comandos interessantes como `cwindowsfeatures featureName` para instalar algum recurso do Windows, ou `cpython packageName` para quem brinca com Phyton e `cgem packageName` para quem brinca com Ruby Gems.

Para mais informações sobre os comandos disponíveis acesse [este link](https://github.com/chocolatey/chocolatey/wiki/CommandsReference).

### Tips

**Listando as apps instaladas**
Uma dificuldade que tive foi em listar todas as apps instaladas em minha máquina.
Depois de uma rápida pesquisa encontrei [este link](https://github.com/chocolatey/chocolatey/issues/125).

`cver all -localonly` para listar todas as apps instaladas.
