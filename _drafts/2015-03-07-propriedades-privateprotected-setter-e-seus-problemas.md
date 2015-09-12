---
layout: post
title: Propriedades private-protected setter e seus problemas
tags: csharp
published: True
---

Escrevo este post mais como uma dúvida de arquitetura do que como um tutorial ou artigo. Espero gerar uma discussão sobre o assunto com devs/arquitetos que tenham ou tiveram a mesma dúvida.

Foi criado um projeto de exemplo no GitHub mostrando o problema. Para exemplificar vamos estruturar nossa aplicação:

## Camada de domínio

Em nossa aplicação, todas as nossas classes de domínio implementam IEntity:

``` csharp
public interface IEntity
{
    int Id { get; }
}
```

> O Id é gerado pelo banco de dados.

Classes auditáveis implementam IAuditable:

``` csharp
public interface IAuditable
{
    DateTime CreateAt { get; }
    IUser CreateBy { get; }
    DateTime? UpdateAt { get; }
    IUser UpdateBy { get; }
}
```

> Os campos CreateAt e UpdateAt são gerados pelo banco de dados.

Repare que nossa interface expõe apenas o get. Isto é proposital pois não queremos que nossos usuários manipulem estas propriedades já que a responsabilidade de gerar estes dados é do banco de dados.

Algumas classes de nosso domínio:

``` csharp
public class CheckListItemTemplate : IEntity, IAuditable
{
    public virtual int Id { get; protected set; }
    public virtual string Texto { get; set; }
    public virtual int? Ordem { get; set; }
    public virtual IList Itens { get; protected set; }
    public virtual CheckListItemTemplate Parent { get; protected set; }

    public virtual DateTime CreateAt { get; protected set; }
    public virtual IUser CreateBy { get; set; }
    public virtual DateTime? UpdateAt { get; protected set; }
    public virtual IUser UpdateBy { get; set; }
    public virtual Passo Passo { get; set; }

    public CheckListItemTemplate()
    {
        Itens = new List();
    }

    public void AddItem(CheckListItemTemplate item)
    {
        item.Parent = this;
        Itens.Add(item);
    }
}

public class Passo : IEntity
{
    public int Id { get; protected set; }
    public virtual IList CheckListItens { get; protected set; }
}

public class Processo : IEntity
{
    public virtual int Id { get; protected set; }
    public virtual Passo Passo { get; set; }
    public virtual IList CheckListItens { get; protected set; }
}
```

Passo.CheckListItemTemplate é uma lista de itens templates e Processo.CheckListItens é uma lista de itens em execução.

> Vamos utilizar o NHibernate como ORM. Por isso o porque das propriedades serem virtuais.

## Projeto Web (UI)

No projeto web teremos as ViewModels IViewEntity que basicamente serão utilizadas para exibir/manipular dados nas views.

``` csharp
public interface IViewEntity
{
    int Id { get; set; }
}
```

Para converter de IEntity para IViewEntity (ou vice-versa) vamos utilizar o AutoMapper.
Pensando em reaproveitamento teremos um controller base para operações de CRUD:

``` csharp
[Authorize]
public abstract class baseEntityController : Controller
    where TModel : class, BindSolution.Framework.Domain.IEntity
    where TView : class, BindSolution.Framework.Web.IViewEntity, new()
{
    public IRepository Repository { get; private set; }
    public IMappingEngine Mapper { get; private set; }

    public baseEntityController(IRepository repository, IMappingEngine mapper)
    {
        Repository = repository;
        Mapper = mapper;
    }

    [ValidateJsonAntiForgeryToken, HttpPost]
    public virtual ActionResult salvar(TView vm)
    {
        if (ModelState.IsValid)
        {
            TModel model;
            if (vm.IsNew)
            {
                model = Mapper.Map(vm);
                AddEntity(model, vm);
            }
            else
            {
                model = Repository.Get(vm.Id);
                Mapper.Map(vm, model, typeof(TView), typeof(TModel));
                SaveEntity(model, vm);
            }

            return RedirectToAction("index");
        }

        Response.StatusCode = 400;
        return View("editor", vm);
    }
}
```

A baseEntityController terá algumas actions* como index, novo, editar e deletar e salvar (esta últma mostrada acima). As duas actions novo e editar o post irá para salvar.

Desta forma nossos controllers ficaram bem enxutos:

``` csharp
public class processosController : baseEntityController
{
    public override void AddEntity(Processo model, ProcessoViewModel viewModel)
    {
        model.Passo = PassoRepositorio.All().OrderBy(p =&gt; p.Ordem).ThenBy(p =&gt; p.CreateAt).First();
        model.CheckListItens.Clear();
        foreach (var item in Mapper.Map&gt;(model.Passo.CheckListItens))
            model.AddCheckListItem(item);
        base.AddEntity(model, viewModel);
    }
}
```

Neste caso, precisávamos de que após salvar o processo, o primeiro Passo (ordenado pelo campo Ordem seguido do campo CreateAt) seja associado ao processo, então tivemos apenas que sobrescrever o método AddEntity.
O AutoMapper ficará encarregado de converter um CheckListItemTemplate para um CheckListItem.

> *Actions foram omitidas

## Projeto de testes

Para nossos testes vamos utilizar um repositório fake Repository:

``` csharp
public class Repository : IRepository
    where T : class, IEntity
{
    private readonly IDictionary _context = new Dictionary();

    public void Delete(T obj)
    {
        _context.Remove(obj.Id);
    }

    public void Store(T obj)
    {
        if (obj.Id &gt; 0)
            _context[obj.Id] = obj;
        else
        {
            var generateId = _context.Values.Any() ? _context.Values.Max(p =&gt; p.Id) + 1 : 1;
            var stub = Mock.Get(obj);
            stub.Setup(s =&gt; s.Id).Returns(generateId);
            _context.Add(generateId, stub.Object);
        }
    }

    // ..
}
```

Em nossa camada de repositório o NHibernate é responsável por atualizar os dados da camada de domínio como é o caso da propriedade Id. Porém na camada de testes não existe (nem deve existir) qualquer ORM para realizar esta tarefa, então temos de gerenciar isto manualmente.

No método Store*, todos os objetos de testes (do tipo IEntity) devem ser um Mock*, caso contrário irá gerar uma exception na linha Mock.Get(obj).
Isto porque não temos como setar a propriedade Id com um novo valor**, a solução encontrada foi mocar o objeto inteiro para que o Get da propriedade Id correspondesse ao novo Id. Exatamente o que faz esta linha stub.Setup(s =&gt; s.Id).Returns(generateId).

> *Por convenção, objetos com Id <= 0 são novos e com Id > 0 são objetos já existentes no banco de dados.
*Como mock utilizo o Moq.
**A propriedade Id em nosso domínio esta como protected setter

### Escrevendo um teste

Para qualquer teste onde tenhamos de criar um novo processo utilizando o controller processosController, obrigatoriamente teremos que ter nosso repositório PassoRepositorio com alguns dados fictícios, caso contrário irá gerar uma exception ao tentar associar o primeiro passo ao novo processo na linha .First() em nosso controller.
Esta exception é proposital afinal o passo atual é obrigatório para um processo.

Vamos configurar o TestInitialize para popular nosso repositório com alguns dados fictícios:

``` csharp
var fix = new Fixture();
var listPassos = fix.Build&gt;()
                            .Do((passo) =&gt; {
                                passo.SetupProperty(x =&gt; x.Nome, fix.Create());
                                passo.SetupGet(x =&gt; x.CheckListItens).Returns(
                                    fix.Build()
                                        .With(p =&gt; p.Texto)
                                        .OmitAutoProperties()
                                        .CreateMany(5).ToList()
                                    );
                            })
                            .OmitAutoProperties()
                            .CreateMany(10);

foreach (var item in listPassos)
    passoRepository.Store(item.Object);
```

> Fixture é uma biblioteca chamada AutoFixture que nos ajuda a criar dados fictícios.
Neste caso estamos criando uma lista de 10 passos cada um com 5 CheckListItemTemplate.

Com nosso repositório preenchido, podemos criar nosso teste:

``` csharp
[TestMethod]
public void Salvar_novo_processo_modificar_data_atendimento_passo_atual()
{
    // Arrange
    var fix = new Fixture();
    var vm = fix.Create();

    //Act
    Controller.salvar(vm);
    var processo = Repository.Get(p =&gt; p.DataEntrada == vm.DataEntrada &amp;&amp; p.ProximoAtendimento == vm.ProximoAtendimento);

    //Asserts
    processo.Should().NotBeNull();
    processo.Passo.Should().NotBeNull();
}
```

### O que está errado

Lembre-se que o responsável por criar objetos no meu baseEntityController utiliza o AutoMapper para converter um objeto IViewModel para um objeto IEntity, repare na linha model = Mapper.Map(vm);
O problema é que o repositório Fake não consegue salvar um objeto IEntity, apenas um Mock.

O maior problema ocorre por causa do protected setter na propriedade Id. Quando falamos a nível de designer é uma ótima abordagem, porém isto traz enormes transtornos quando vamos testar nossa aplicação.

Com esta abordagem, em nosso projeto de testes, temos de mocar qualquer objeto IEntity, para ser capaz de setar o Id, além de termos que nos preocupar com nossa camada de interface como é o caso do controller base que utiliza o AutoMapper.
