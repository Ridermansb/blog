---
layout: post
title: Testes de interface com PageObjects e Selenium
published: true
tags: pageobjects selenium test tdd
---

Você ja ouviu falar sobre [PageObjects](https://code.google.com/p/selenium/wiki/PageObjects) ?

Neste artigo, pretendo apresentar o básico sobre PageObjects e como este pattern (sim ele e um pattern) pode nos ajudar com nossos testes de interface no selenium.

# O problema
Sempre que criamos testes de interface, por mais oranizado que sejamos, com o passar do tempo teremos dificuldades para entender os testes e fazer adaptações/melhorias.
Imagine uma tela com 20 campos, abas, integrações via REST, campos calculados etc.. como seria os testes para esta página?

Imagine isto por todo o código:

{% highlight C# %}
driver.FindElement(By.Name("userName")).SendKeys(UserName);
driver.FindElement(By.Name("password")).SendKeys(Password);
driver.FindElement(By.Name("login")).Click();
{% endhighlight %}

O problema deste tipo de codigo é a poluição, `driver`, `FindElement`, `By.Name`, `SendKeys` não diz nada para o nosso negócio!

# O que é PageObjects
PageObjects é um pattern que define uma classe para cada página de nossa  aplicação, desta forma podemos abstrair detalhes de implementação facilitando os testes.
A ideia e simples, em uma página de Logon, temos os campos Login e Senha, neste caso teremos uma class `LoginPage` com as propriedades `Login` e `Senha` representando cada campo e um método chamado `Logar` representando a ação de logar. Assim teremos as funcionalidades da nossa pagina abstraidas em uma classe.

# Exemplo prático
Vamos testar uma tela simples de logon, com dois inputs `login` e `senha`  e um botão de login.

## Specs
Temos de testar as 3 situações, usuário não digitou nenhum dado, usuário digitou dados incorretos ou usuário digitou dados corretos. Todas descritas na epsecificação abaixo:

{% highlight Gherkin (Cucumber) %}
Feature: Logon

Scenario Outline: Ao clicar em logon sem informar usuário e senha o sistema deve exibir uma mensagem
    Given Eu acessei o site com o navegador <browser>
    When Eu pressionar Logon
    Then Deve ser exibido a mensagem Campo usuário é obrigatório
    And Deve ser exibido a mensagem Campo senha é obrigatório

Scenario Outline: Ao clicar em logon com usuário e senha inválidos
    Given Eu acessei o site com o navegador <browser>
    And Eu inseri os dados de acesso UsuarioFake e SenhaFake
    When Eu pressionar Logon
    Then Deve ser exibido a mensagem usuário e/ou senha inválido(s)

Scenario Outline: Ao clicar em logon
    Given Eu acessei o site com o navegador <browser>
    And Eu inseri os dados de acesso UsuarioOk e SenhaOk
    When Eu pressionar Logon
    Then Deve redirecionar para a página inicial
{% endhighlight %}

## Steps

Para exemplificar, optei por mostrar os steps primeiro para demonstrar como o código fica mais limpo e fácil de ler usando PageObjects:

Repare que a ação de clicar em um botão no site é um método em nossa classe, facilitando bastante a leitura e o entendimento dos nossos testes.

{% highlight C# %}
[Given(@"Eu acessei o site com o navegador (.*)")]
public void GivenEuAsseseiOSistemaComONavegador(string browser)
{
    CurrentBrowser = browser;
    CurrentPageObject = new LogonPage(WebBrowser.Current);
}

[Given(@"Eu inseri os dados de acesso (.*) e (.*)")]
public void GivenEuInseriOsDadosDeAcesso(string usuario, string senha)
{
    CurrentPageObject.Usuario = usuario;
    CurrentPageObject.Senha = senha;
}

[When(@"Eu pressionar Logon")]
public void WhenEuPressionarLogon()
{
    CurrentPageObject.Logon();
}

[Then(@"Deve ser exibido a mensagem (.*)")]
public void ThenDeveSerExibidoAMensagem(string mensagem)
{
    CurrentPageObject.Alerts.Any(p => p.Mensagem == mensagem).BeTrue();
}

[Then(@"Deve redirecionar para a página inicial")]
public void ThenDeveRedirecionarParaAPaginaInicial()
{
    return WebBrowser.Current.Title.Contains(PageTitle);
}
{% endhighlight %}

## PageObjects
Aqui teremos nossa classe com todos os detalhes de implementação da página:
Optei por exibir apenas o que é importante com o objetivo de melhorar a didática deste artigo.

{% highlight C# %}
public class LogonPage
{
    private IWebDriver driver;

    [FindsBy(How = How.Name, Using = "usuario")]
    private IWebElement usuarioElement;

    [FindsBy(How = How.Name, Using = "senha")]
    private IWebElement senhaElement;

    [FindsBy(How = How.Name, Using = "logon")]
    private IWebElement logorElement;

    public string Usuario { get { return usuarioElement.Text; } set { usuarioElement.SendKeys(value); } }
    public string Senha { get { return senhaElement.Text; } set { senhaElement.SendKeys(value); } }

    public LogonPage(IWebDriver drive)
    {
        PageFactory.InitElements(driver, this);
    }

    public void Logon()
    {
        logorElement.click()
    }
}
{% endhighlight %}

# Conclusão
Programar é a arte de simplificar as coisas, não é o que você consegue fazer com uma tecnologia foda, é como você usa esta tecnologia para simplificar ainda mais o seu código.
PageObjects tem um poder incrível, com este pattern podemos melhorar e muito a manutenabilidade de nossos testes. Isto nos deixa mais animados a escrever ainda mais testes e consequentimente melhorando a cobertura de testes em nosso código.
Pense por exemplo em quando você tem de adicionar um novo campo no html, e esta alteração quebra nossos testes. Com PageObjects, resolver estes tipos de problemas fica bem mais simples e pontual.

## Reference

 * [Implementing the Page Object Pattern in C# using Selenium WebDriver](http://www.paulsodimu.co.uk/Post/Implementing-the-Page-Object-Pattern-in-C-using-Selenium-WebDriver)
 * [Behavioural testing in .Net with SpecFlow and Selenium (Part 2)](http://jamesheppinstall.wordpress.com/2012/10/28/behavioural-testing-in-net-with-specflow-and-selenium-part-2/)
 * [PageObjects](https://code.google.com/p/selenium/wiki/PageObjects)
 * [Page Object Pattern](http://assertselenium.com/automation-design-practices/page-object-pattern/)
 * [Selenium PageObjects and PageFactory](http://relevantcodes.com/pageobjects-and-pagefactory-design-patterns-in-selenium/)
