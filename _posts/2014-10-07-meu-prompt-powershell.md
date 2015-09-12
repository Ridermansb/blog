---
layout: post
title: Meu prompt powershell
published: true
date: 2014-10-07
tags: cmd powershell
---

Você acha útil exibir todo o caminho no prompt? Pense bem, quando foi que esta informação te ajudou em algo?
Quando navego pelo cmd, normalmente eu quero ir a algum lugar e não saber de onde vim! Afinal entende-se que eu seu de onde vim :).

Prompt original:

![Prompt original com Posh-GIT](http://i.imgur.com/yS3r8fe.png)

**Com [Posh-GIT](http://bit.ly/1sbZ5Mh)**

Eu nunca gostei do prompt, então fiz algumas pesquisas, inclusive no [stackoverflow](http://bit.ly/1xniLwG), e consegui chegar a algo que me agradou (obrigado [@mmozuras](https://github.com/mmozuras/powershell/blob/master/PromptConfig.ps1)).

## Entendendo o PowerShell
O PowerShell possui um arquivo `ps1` que é executado sempre que você abre uma nova janela. Este arquivo é o seu Profile e para descobrir sua localização é só você digitar `$PROFILE` no PowerShell que o path será exibido.


## Configurando seu profile
Agora que já sabemos a localização do arquivo de profile, temos apenas de configurá-lo.

Abra o arquivo com um bloco de notas.

> Caso você tenha instalado o `Posh-GIT` ou algum module, é bem provável que nas primeiras linhas esteja importando os modulos. Algo como: `. '{Module-Path}\posh-git\profile.example.ps1'`



Para configura nosso prompt temos apenas de criar uma function chamada `prompt`.

{% highlight PowerShell %}
...

function prompt {
    $path = ""
    $pathbits = ([string]$pwd).split("\", [System.StringSplitOptions]::RemoveEmptyEntries)

    if($pathbits.length -eq 1) {
        $path = $pathbits[0] + "\"
    } else {
        $path = $pathbits[$pathbits.length - 1]
    }

    $host.UI.RawUi.WindowTitle = $path

    Write-Host($path) -nonewline -foregroundcolor Yellow

    $Global:GitStatus = Get-GitStatus
    Write-GitStatus $GitStatus

    Write-Host('>') -nonewline -foregroundcolor Yellow

    return " "
}

Enable-GitColors
{% endhighlight %}


## Referências

* [powershell / PromptConfig.ps1](https://github.com/mmozuras/powershell/blob/master/PromptConfig.ps1)
