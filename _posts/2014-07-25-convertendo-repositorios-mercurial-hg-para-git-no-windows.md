---
layout: post
title: Convertendo repositórios mercurial (HG) para git (no windows)
published: True
tags: mercurial hg git
---

Para exportar repositórios Mercurial para Git vamos precisar de uma ferramenta chamada [fast-export](git clone git://repo.or.cz/fast-export.git);  

A melhor forma de obter, é clonar o repositório:

    git clone git://repo.or.cz/fast-export.git

Inicialize um repositório git

    git init src.git

Use o fast-export para exportar o projeto

    cd src.git
    hg-fast-export.sh -r


# Windows

Ao tentar realizar esta tarefa no Windows tive alguns problemas (listados abaixo), uma forma de contornar é criar uma VM (cloud ou local usando o VirtualBox).


**VM**

* Crie uma VM e contecte-se nela.
* Instale o git e o mercurial (`sudo apt-get install git mercurial`)
* Clone o repositório hg-fast-export `git clone https://github.com/frej/fast-export.git`

**Com a VM configurada:**

* Clone o repositório mercurial que deseja converter `hg clone https://your/hg/repo/address/your-repo-hg`
* Crie uma pasta para o repositório git `mkdir your-repo-git`
* Entre nesta pasta `cd your-repo-git`
* Inicialize o git `git init`
* Converta `../fast-export/hg-fast-export.sh -r ../your-repo-hg/`

**Publicando novo repositório GIT**

* Adicione origin para o novo repositório `git remote add origin https://your/git/repo/address`
* Push todos os branches e tags `git push -u origin --all`


E por último, lembre-se de mirar seu `.hgignore` para o equivalente `.gitignore`


## Principais problemas


### Ao executar hg-fast-export.sh


> Branch name doesn't conform to GIT standards: refs/heads/master'  

[Para corrigir](http://stackoverflow.com/questions/9537454/how-to-solve-hg-fast-export-error-branch-name-doesnt-conform-to-git-standards), edite o arquivo `hg-fast-export.py`.


    if sys.platform == "win32":
        import os, msvcrt
        msvcrt.setmode(sys.stdout.fileno(), os.O_BINARY)


------------


> 'ImportError: No module named mercurial'

Significa que o Python não reconhece o Mercurial. Para corrigir, instale o Mercurial.

`easy_install -U mercurial`

### Ao instalar mercurial


> Setup script exited with error: Unable to find vcvarsall.bat

Para corrigir, crie um arquivo `distutils.cfg` em `%PYTHONHOME%\Lib\distutils` com o seguinte conteúdo:

    [build]
    compiler=mingw32

Instruções [neste link](http://mercurial.selenic.com/wiki/Download#Using_easy_install).


## Fonte

* [Converting From Mercurial To Git](http://hivelogic.com/articles/converting-from-mercurial-to-git/)
* [hg-fast-export: convert Mercurial repositories to git repositories](http://hedonismbot.wordpress.com/2008/10/16/hg-fast-export-convert-mercurial-repositories-to-git-repositories/)
* [Converting a Mercurial repository to Git on Windows](http://www.frederic-latour.com/blog/converting-a-mercurial-repository-to-git-on-windows/)
* [Convert A Mercurial (hg) Repo To Git, With Full Fidelity, On Any Os](http://blog.tatham.oddie.com.au/2014/03/24/nerd-corner-convert-a-mercurial-hg-repo-to-git-with-full-fidelity-on-any-os/)
