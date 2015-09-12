---
layout: post
title: Cassette - Assets manager para Asp.net MVC
published: True
tags: cassette aspnet-mvc
---

Neste post vou apresentar o [Cassette](http://getcassette.net/), uma alternativa ao [asset manager da Microsoft](https://www.nuget.org/packages/microsoft.aspnet.web.optimization/) que inclusive o André Paulovich fez uma ótima [introdução aqui no 100loop](http://www.100loop.com/destaque/tecnicas-de-bundling-em-asp-net/).


# O que o Cassette se propõe a resolver?

Quando desenvolvemos uma página web, vários arquivos podem ser carregados para compor a página. Se analisarmos a aba Network do [DevTools](https://developer.chrome.com/devtools/index#improving-network-performance) veremos quais arquivos.

Quanto mais arquivos necessários para compor a página, mais arquivos devem ser baixados pelo cliente.
Quanto maior estes arquivos, maior o tempo de download e processamento destes arquivos.

Cassette se propõe a resolver este problema compactando e minificando arquivos javascript e styles (css).

# Apresentando

## Instalação

Bem simples:

    Install-Package Cassette.Aspnet

## Configuração

Após a instalação, será criado um arquivo `CassetteConfiguration.cs` onde poderemos configurar nossos assets. É nele que configuramos como nossos assets estão organizados em nosso projeto e como iremos carregá-los.

Podemos por exemplo criar um bundle com todos nossos css:

``` chsarp
bundles.Add<StylesheetBundle>("~/Content", new FileSearch {
    SearchOption = SearchOption.TopDirectoryOnly,
    Pattern = "*.css"
});
```

Mais informações sobre como configurar bundles [neste link](http://getcassette.net/documentation/v2/bundle-configuration).


## Views

**Ao invés** de fazermos as chamadas tradicionais aos assets (js/css) como:

``` html
<link href="~/Content/bootstrap.min.css" rel="stylesheet" type="text/css" />
```

Faremos estas chamadas através de Bundles, para isto devemos fazer uma referência ao bundle que desejamos carregar:

``` csharp
Bundles.Reference("Content");
Bundles.Reference("Scripts");
```

E indicar o local que será inserido na página:

``` html
    ....
    @Bundles.RenderStylesheets()
</head>
<body>
...
    @Bundles.RenderScripts()
</body>
```

## Ordem

É comum que alguns assets devam ser carregados primeiro que outros. Com o Cassete podemos definir esta ordem de várias maneiras.

**Nas configurações dos bundles:**

``` csharp
  bundles.Add<StylesheetBundle>("~/Scripts",
      "~/Scripts/jquery.js"
      , "~/Scripts/bootstrap.js"
      , "~/Scripts/app.js"
  );
```

**Via asset references**

No arquivo desejado, neste caso o `app.js`:

``` javascript
// @reference jquery.js bootstrap.js
```

Mais informações [neste link](http://getcassette.net/documentation/v2/asset-references).

**Via Bundle descriptor file**

Um arquivo `bundle.txt` é criado na pasta origin (no nosso caso em `Scripts`) indicando a ordem dos arquivos:

    jquery.js
    bootstrap.js
    app.js

Mais informações [neste link](http://getcassette.net/documentation/v1/configuration/bundle-descriptor-file).


## Em produção

Cassete concatena e minifica apenas quando entende que o projeto está em modo produção:

``` xml
<system.web>
    <compilation debug="false">
```

Isto permite que possamos debugar nossos js/css facilmente em ambiente de desenvolvimento, sem nos preocupar em concatenar e minificar estes arquivos em produção.

## Plugins

Uma das grandes vantagens do Cassette são os plugins.

Para quem utiliza coffee, podemos delegar a responsabilidade de compilar para o Cassette com o pacote [Cassette.CoffeeScript](https://www.nuget.org/packages/Cassette.CoffeeScript/)

O mesmo se aplica ao sass com o pacote [Cassette.Sass](https://www.nuget.org/packages/Cassette.Sass/)

Veja a lista completa de plugins disponíveis [neste link](http://getcassette.net/documentation/v2/plugins).

# Indo além

Cassette vai mais além do que apenas javascripts e styles, também possui um excelente suporte a [html templates](http://getcassette.net/documentation/v2/html-templates), [css sprints](http://getcassette.net/documentation/v2/stylesheets/image-spriting), integração com [MSBuild](http://getcassette.net/documentation/v2/msbuild).

Versionamento dos arquivos: No final de cada arquivo/bundle é adicionado um hash. Assim o asset pode ser cacheado no cliente e qualquer modificação, um novo hash será gerado forçando o cliente a baixar novamente o arquivo.

Cassette.axd: Em produção você pode acessar: `http://{url-do-seu-projeto}/cassette.axd`, isto irá exibir seus bundles carregados pelo Cassette. Ajuda bastante se quisermos entender como está configurado nossos bundles no projeto.

Se você já utiliza o Web.Optimization da Microsoft, recomendo a [leitura deste artigo](http://codewith.us/asp-net-bundling/), um comparativo entre as duas bibliotecas.

# Conclusão

Com certeza temos muito mais para apresentar sobre o Cassette, porém neste post meu objetivo era apenas apresentar um pouco sobre esta incrível ferramenta.
Os benefícios vão muito além de a simples concatenação e minificação de arquivos!

Espero que tenham gostado.
