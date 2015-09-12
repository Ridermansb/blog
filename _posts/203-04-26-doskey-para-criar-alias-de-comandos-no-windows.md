---
layout: post
title: Doskey para criar alias de comandos no Windows
published: true
date: 2013-04-26
tags: windows command-line doskey
---

Eu confesso, sou fã do MacOS e do Ubuntu. Em termos de designer os dois deixaram o Windows XP/Vista/7/8 e acredito que até o 9 (quando sair rs) bem para trás. Em termos de recursos e usabilidade eu não sei, nunca trabalhei efetivamente com nenhum dos dois. Meus conhecimentos se limitam à VMs de testes que instalei para conhecer os dois sistemas operacionais.

O pouco que conheço dos recursos, já sinto falta no Windows, por exemplo o apt-get do Linux. Uma ótima ferramenta para você instalar e atualizar aplicativos via linha de comando.
Felizmente, existem soluções relativamente semelhantes para Windows (no caso do apt-get, o [Chocolatey por exemplo]({% post_url 2014-04-04-app-get-para-windows-conheca-o-chocolatey %}).

Neste post vou mostrar como utilizar o `doskey`, uma ferramenta nativa do Windows para criar alias de comandos DOS (igualmente o comando `alias` no MacOS e Linux).

# Problema

Em diversas ocasiões, quando trabalhamos via linha de comandos, temos de repetir diversos comandos várias e várias vezes. Exemplo, se quisermos exibir apenas os subdiretórios de uma pasta, ordenados pelo nome em uma visualização lado a lado.

`dir /AD /ON /W`

![Dir para listar diretórios ordenados em ordem alfabética no DOS]({{ site.assets }}/doskey-para-criar-alias-de-comandos-no-windows/dir.jpg)

Imagine se toda vez que desejarmos listar os diretórios desta forma, tivermos de digitar todo o comando novamente.
Ficaria inviável.

**No Mac** existe um comando chamado `alias`, onde basicamente você cria um alias para executar um comando.
Exemplo: `alias documents='cd ~/Documents'`, sempre que digitar `documents` o comando `cd ~/Documents` será executado.

**No Linux** `alias lls=ls -l`, sempre que digitar `lls` o comando `ls -l` será executado.

**No Windows** ? [doskey](http://technet.microsoft.com/en-us/library/cc753867(v=ws.10).aspx).

Neste post vou explicar como configurar o doskey para listar os diretórios em ordem alfabética e lado a lado com o comando `dird`.

# Solução
Doskey realiza a mesma função que o `alias` do Linux/Mac, sua utilização é bem parecida `dosley <alias>=<comando>`. Então vamos criar nossa primeira alias:
Com o DOS aberto digite:

`doskey dird=dir /AD /ON /W`  e teste o comando `dird`

Funcionou! :)

Agora, feche e abra uma nova janela do DOS e tente executar o comando novamente.
Um erro de `'dird' is not recognized as an internal or external command...`. Isto ocorre porque o comando que criamos fica ativo apenas no ambiente atual do DOS. Se fecharmos o DOS, todos estes comandos se perdem.

Bom, não queremos parte da solução, e sim a solução completa.

## Persistir um comando no DOS

Para que nossos comandos não se percam, precisamos persistir os comandos no ambiente do DOS. Para realizamos esta tarefa vamos precisar do [regedit](http://www.wikihow.com/Use-Regedit).

Basicamente, sempre que uma janela do DOS for aberta, um arquivo `autorun.bat` será executado automaticamente criando nossos alias.

 * Crie um novo arquivo chamado `autorun.bat`.
 * Adicione o comando para criar nossa alias: `doskey dird=dir /AD /ON /W` no arquivo `autorun.bat` e salve em uma pasta qualquer na sua máquina.
 * Abra o regedit e procure pela entrada `HKEY_CURRENT_USER\Software\Microsoft\Command Processor`
 * Adicione uma nova entrada com o nome `AutoRun` e o valor como o caminho completo para nosso arquivo `autorun.bat`.

![Editando o regedit para persistir o doskey]({{ site.assets }}/doskey-para-criar-alias-de-comandos-no-windows/regedit.jpg)

Agora é só testar, feche o DOS e abra novamente.
Repare que sempre que você abre uma nova janela do DOS, o autorun é executado. Persistindo assim os comandos do doskey.

*Uma dica, é adicionar no início do `autorun.bat` o comando `@echo off` para evitar que sejam exibidos os comandos executados pelo `autorun.bat`. Não se esqueça de restaurar as configurações do echo para `@echo on` no final do arquivo.*

    @echo off
    doskey dird=dir /AD /ON /W
    @echo on

## Opções de execução do doskey

Na [página da ferramenta](http://technet.microsoft.com/en-us/library/cc753867(v=ws.10).aspx) está documentado as opções disponíveis para o `doskey` como:

 * `doskey /macros`: Lista todas as macros configuradas
 * `doskey /history`: Histórico de comandos executados no DOS

# Alias para Sublime Text 2

Minha real necessidade com este recurso não foi listar de forma amigável os diretórios :), e sim executar o [Sublime Text 2](http://www.sublimetext.com/2) já com a pasta atual como projeto.

O doskey que utilizei foi: `doskey subl="C:\Program Files\Sublime Text 2\sublime_text.exe" $*`.

*`$*` representa todos os parâmetros passados pela execução, exemplo: `subl "c:\ab" parametro2`*

# Referências:

 * [Permanent Windows command-line aliases with doskey and AutoRun](http://darkforge.blogspot.com.br/2010/08/permanent-windows-command-line-aliases.html)
 * [Sublime Text from Command Line (Win7)](http://stackoverflow.com/questions/9440639/sublime-text-from-command-line-win7)
