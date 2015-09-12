---
layout: post
title: Granvando CD-ISO via linha de comando
tags: cdburnerxp command-line iso
published: True

---

Em [minha empresa](http://bindsolution.com.br), existem rotinas de backups automatizados, tanto da base de dados quanto dos arquivos gerados pelo cliente. Sempre que a soma dos backups chegam a 4G um arquivo ISO é criado.

Isto é feito para cada cliente, então vi a necessidade de automatizar esta tarefa e para minha sorte [CD Burner XP](http://cdburnerxp.se/) possui [acesso via linha de comando](http://cdburnerxp.se/help/appendices/commandlinearguments). Vamos entender como funciona.

*Este tutorial entende que você o CDBurnerXP já está instalado em sua máquina*

# Ambiente

## Path do Windows

Para facilitar o acesso vamos adicionar ao *PATH* do Windows o app command line do CD Burner XP

Em `System` &gt; `Advanced system settings` &gt; `Advanced` clique em `Environment Variables...`

Selecione `Path` e clique em `Editar`

![Path Windows para CD Burner XP via command line](http://i.imgur.com/d3BKJaf.jpg)


*Ou `%PROGRAMFILES%\CDBurnerXP\cdbxpcmd.exe`*

## Erro conhecido até a versão 4.5.1.3868

Em minha primeira tentativa o erro abaixo foi exibido:

&gt; Error HRESULT E_FAIL has been returned from a call to a COM component

Pesquisando na internet descobri [este forum](https://forum.cdburnerxp.se/topic/7624-cdbxpcmd-e-fail-from-a-com-component-on-xp-sp2/) e lendo as postagens, aparentemente o erro ocorre quando não há nenhum drive na máquina.
Para solucionar (**temporariamente assim espero**) o problema utilizei a ferramenta [Virtual Clone Drive](http://www.slysoft.com/en/virtual-clonedrive.html) que simula um drive em meu PC.

Caso não tenha nenhum drive de CD ou DVD, instale a ferramenta e adicione um drive.


# Granvando para ISO

Agora estamos prontos para gravar um CD via linha de comando apenas utilizando o comando `cdbxpcmd` em nosso console.

Grava todos os arquivos da pasta especificada em ISO e fecha o disco:

`cdbxpcmd --burn-data -folder:"{Caminho da pasta}" -iso:"{Caminho onde irá salvar o ISO}.iso" -name:"{Nome do disco}" -close`

# Granvando em CD

Antes de gravar arquivos em um CD precisamos especificar qual driver iremos utilizar. Para isto, `cdbxpcmd  –list-drives` irá listar todos os drivers instalados **e um código**:

![Listar drivers via linha de comando com CD Burner XP](http://i.imgur.com/65UuA9J.jpg)

Este código serve para referenciar um drive, exemplo: `-drive:0`.

Então, o processo é o mesmo porém ao invés de especificarmos `-iso` iremos especificar o driver desejado `-drive:0`.

`cdbxpcmd --burn-data -folder:"{Caminho da pasta}" -drive:0 -name:"{Nome do disco}" -close`

Espero que tenham gostado.
