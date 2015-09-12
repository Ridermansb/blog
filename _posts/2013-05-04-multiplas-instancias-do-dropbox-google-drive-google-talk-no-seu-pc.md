---
layout: post
title: Múltiplas instâncias do Dropbox, Google Drive, Google Talk no seu PC
tags: dropbox google-drive
published: True
---

Hoje vou mostrar como executar duas (ou mais) contas do Dropbox, Google Talks e Google Drive.

O principal motivo de não conseguirmos rodar duas instâncias do Dropbox por exemplo é pelo fato de que ao executar a aplicação ela será executada com o contexto do usuário logado. Sabendo disto, tudo o que temos de fazer é executar a app em um contexto de outro usuário.

Em outros casos, como o Google Talk, a opção simplesmente está desabilitada por padrão. Tudo o que temos de fazer é habilita-la.

# Criando o ambiente

Primeiramente precisaremos de um outro contexto (usuário) para executar a aplicação. Então crie um novo usuário **com senha** no seu Windows. Não precisa ter privilégios administrativos!

Para referência futuras, chamaremos este usuário de *Usuário secundário*.

![Tipo de conta para rodar múltiplas contas](http://i.imgur.com/0NZeqg6.png
)

# Dropbox e Google Drive

## Instalação

**Efetue o login com o usuário secundário**, baixe e instale o Dropbox e o Google Drive

Lembre-se de selecionar um local acessível aos dois usuários, para isto utilize as configurações avançadas para definir onde a app irá sincronizar os arquivos.

![Configurações avançadas para selecionar a pasta de sincronização](http://i.imgur.com/zjgFLjj.png)

Recomendo selecionar uma pasta em uma outra partição do HD.

## Rodar a app em um contexto de outro usuário

A grande jogada é executar as apps no usuário principal porém com o contexto do nosso usuário secundário. Para isto vamos utilizar um comando do Windows chamado `runas`.

Exemplo: `runas /user:USERNAME /savecred c:/users/usuario-secundario/path/to/dropbox/google-drive.exe`

Este comando permite que rodemos aplicações como se fossemos outro usuário.

### Como localizar a pasta de instalação?

Acesse o *Task Manager* (Ctrl + R, digite `taskmgr`) localize o processo *Dropbox (32 bit)* e *Google Drive (32 bit)* e com o botão direito escolha *Open File Location*.

![File location do Dropbox](http://i.imgur.com/kI5JQf2.jpg)

## Atalho

**Retorne ao usuário principal** e navegue até o diretório de instalação (que buscamos no passo anterior) e crie um atalho no Desktop.

![Atalho para o Dropbox](http://i.imgur.com/NAE612f.jpg)

Agora temos de configurá-lo para executar as apps contexto do usuário secundário.
Com o botão direito, modifique a propriedade *Target* do atalho para:

`runas /user:"{Usuario Secundario Aqui}" /savecred "c:/path/to/file.exe"`

Isto fará com que a app seja excutada em um outro contexto.

Repita estes passos para o Dropbox e Google Drive.

# Google Talk

O Google Talk é mais simples!
Por padrão a aplicação não permite múltiplas instâncias, para habilitar esta opção temos de adicionar o parâmetro `/nomutex` no path de execução da app.
Modifique o atalho do seu Google Talk para:

![Modificando o atalho do Google Talk para aceitar múltiplas instâncias](http://i.imgur.com/7boHmVQ.jpg)

Agora sempre que você executar o Google Talk, outra instância da app será aberta, permitindo logon com vários usuários.

# Referências
 1. [MULTIPLE DROPBOX INSTANCES ON WINDOWS](http://semi-legitimate.com/blog/item/multiple-dropbox-instances-on-windows-7)
 2. [How to Run Multiple Instances of Dropbox in Windows 7](http://www.maketecheasier.com/run-multiple-instances-of-dropbox-in-windows-7/2010/05/21)
 3. [How to Use Multiple Dropbox Accounts on One Computer](http://lifehacker.com/5971204/run-multiple-dropbox-accounts-on-one-computer)
 4. [Running Multiple Instances of Google Talk (GTalk Polygamy) and Login as Multi Users](http://www.mydigitallife.info/running-multiple-instances-of-google-talk-gtalk-polygamy-and-login-as-multi-users/)
 5. [Switch between multiple Google Drive accounts
](https://plus.google.com/114892703028341590446/posts/1mYzdE5KKgv)
