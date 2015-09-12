---
layout: post
title: Get Starter with Rust Lang
tags: [get-starter rust]
published: True
---

Aprender novas linguagens sempre é um

 let (x, y) = (1, 2);

String interpolation: println!("The value of x is: {}", x)

expression-based: let y = if x == 5 { 10 } else { 15 };

Enum

    enum Character {
        Digit(i32),
        Other,
    }

// Bem melhor q case, swith ..
match character {
        Character::Digit(x) =>  println!("The digit is {}", x),
        Character::None => println!("None value selected"),
    }




## Install
A instalação é bem simples, apenas baixe o pacote disponível na [página oficial](http://www.rust-lang.org/install.html) e instale em sua plataforma (no meu caso Windows).

A instalação deverá adicionar ao `Path` do windows o caminho para a pasta bin, algo como `C:\Rust\bin`.

Feito isso, abra o prompt de command e verifique se está ok:

{% highlight Bash %}
rustc --version
{% endhighlight %}

## Introdução
Arquivos fontes do Rust sempre terminam com `.rs`, quando o arquivo tiver mais de uma palavra, recomenda-se usar `hello_world.rs` ao invés de `helloworld.rs`.

Você define uma função da seguinte forma:

{% highlight Rust %}
fn main() {

}
{% endhighlight %}

A função `main` é o início de todo projeto Rust.
Você pode imprimir algo na tela usando o comando: `println!("Hello, world!");`. Recomenda-se uma edentação de 4 espaços (não tabs).

Observe o `!` ao final da função, o que diz ao Rust para executar a macro função (que é como o Rust executa metaprogramação).

## Compiler

Você pode usar `rustc` para compilar seu arquivo fonte e gerar um `helloworld.exe`. Rust irá gerar um arquivo já na sua plataforma (no meu caso windows).

## Cargo

Cargo é responsável por compilar seu projeto, gerenciar suas dependencias, e compilar suas dependencias.

Cargo utiliza um arquivo de configuração do [tipo TOML](https://github.com/toml-lang/toml) como `Cargo.toml` que define as propriedades do seu projeto:

{% highlight Rust %}
[package]

name = "hello_world"
version = "0.0.1"
authors = [ "Your name <you@example.com>" ]

[[bin]]

name = "hello_world"
{% endhighlight %}

Para compilar seu projeto, simplesmente execute `cargo build`, isto irá gerar os executáveis na pasta `target`

## Variáveis

Variáveis são bem simples em Rust, veja este exemplo:

{% highlight Rust %}
let x: = 5;
{% endhighlight %}

Define uma variável `x` com valor 5; Como rust possui [type inference](http://en.wikipedia.org/wiki/Type_inference) ele irá descobrir qual i tipo da variável.
Porém podemos forçar o tipo:

{% highlight Rust %}
let x: i32 = 5;
{% endhighlight %}

Defta forma, definimos a variável `x` como do tipo `i32` com valor `5`.

Rust consegue trabalhar com [pattern matching](http://en.wikipedia.org/wiki/Pattern_matching), por exemplo, a expressão:

{% highlight Rust %}
let (x, y) = (1, 2);
{% endhighlight %}


irá atualizar a variável `x` com valor `1` e  `y` com valor `2`.

Por padrão variáveis são immutable, isto não irá compilar:

{% highlight Rust %}
let x = 5;
x = 10;
{% endhighlight %}

Para conseguir trabalhar com estas variáveis, você pode usar `mut` apos o `let`:

{% highlight Rust %}
let mut x = 5; // mut x: i32
x = 10;

{% endhighlight %}

isto irá compilar.

## Strings
Rust tem suporte a [String interpolation](http://en.wikipedia.org/wiki/String_interpolation), isto significa que você consegue fazer algo do tipo:

{% highlight Rust %}
println!("The value of x is: {}", x)
{% endhighlight %}

Rust irá substituir `{}` pelo `x`. Você pode inclusive definir um formato, existem vários [formatos disponívels na documentação](http://doc.rust-lang.org/std/fmt/)

## if
If em Rust é simples:

{% highlight Rust %}
let x = 5;
if x == 5 {
    println!("x is five!");
} else {
    println!("x is not five :(");
}
{% endhighlight %}

então vou mostrar os diferenciais:

{% highlight Rust %}
let x = 5;
let y = if x == 5 { 10 } else { 15 }; // y: i32
{% endhighlight %}

Rust trata o `if` como uma expressão, o que significa que ele retorna um valor.

> Isto mostra que Rust é uma linguagem baseada em expressão (expression-based language)

## Functions

{% highlight Rust %}
fn add_one(x: i32) -> i32 {
    x + 1
}
{% endhighlight %}


Para funções Rust não possui tipos por inferência nos parâmetros, obrigado o programador a definir o tipo.
Repare que não existe `return` ou `;`, Rust utiliza a ultima expressão como retorno da função.

Você ainda pode utilziar `return`

{% highlight Rust %}
fn foo(x: i32) -> i32 {
    if x < 5 { return x; }
    return x + 1;
}
{% endhighlight %}


porém este é um estilo mais pobre.

Como mencionado anteriormente, em Rust, `if` é uma expressão então nos permite fazer algo como:

## Comments

Rust tem suporte a dois tipos de comentários, line (usando `//`) e doc (usando `///`).

A principal diferença é que com doc comments você pode gerar uma documentação do seu código. Veja a documentação completa [neste link](http://doc.rust-lang.org/book/documentation.html).

## Compound Data Types

### Tuples

Tuples são listas ordenadas de tamanho fixo.

{% highlight Rust %}
let x = (1, "hello");
let y: (i32, &str) = (1, "hello");
{% endhighlight %}

Os parenteses e a virgula definem o tamanho e os tipos;
Um truque bacana é que podemos usar Tuples como retorno de funções, permitindo uma função retornar mais de um valor:

{% highlight Rust %}
fn next_two(x: i32) -> (i32, i32) { (x + 1, x + 2) }

fn main() {
    let (x, y) = next_two(5);
    println!("x, y = {}, {}", x, y);
}
{% endhighlight %}

### Structs

Structs permite que você tenha *fields* nomeados.

{% highlight Rust %}
struct Point {
    x: i32,
    y: i32,
}

...

let origin = Point { x: 0, y: 0 }; // origin: Point
println!("The origin is at ({}, {})", origin.x, origin.y);
{% endhighlight %}

By convention, structs usa capital letter e camel cased: Ou seja, use PointInSpace, ao invés de Point_In_Space.

### Enums

Enums são com em qualquer outra linguagem:

{% highlight Rust %}
enum Ordering {
    Less,
    Equal,
    Greater,
}
{% endhighlight %}

e sua utilização é bem simples:

{% highlight Rust %}
let ordering = Ordering::Greater

if ordering == Ordering::Less {
    println!("less");
} else if ordering == Ordering::Greater {
    println!("greater");
} else if ordering == Ordering::Equal {
    println!("equal");
}
{% endhighlight %}

Porém com um pequeno e poderoso diferencial:

{% highlight Rust %}
enum Character {
    Digit(i32),
    Other,
}
{% endhighlight %}

Observe o `Digit`, você pode definir o tipo de cada elemento.

## Match

Além do tradicional `if/else`, Rust conta com `Match`

{% highlight Rust %}
let x = 5;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    4 => println!("four"),
    5 => println!("five"),
    _ => println!("something else"),
}
{% endhighlight %}

Além de deixar o codigo mais limpo, existe também um validador. Repare no `_`, ele representa o `else` do `if` porém no match ele é obrigatório.

Muito útil por exemplo com Enuns, lembra no nosso código?

{% highlight Rust %}
let ordering = Ordering::Greater

if ordering == Ordering::Less {
    println!("less");
} else if ordering == Ordering::Greater {
    println!("greater");
} else if ordering == Ordering::Equal {
    println!("equal");
}
{% endhighlight %}

Com match seria desta forma:

{% highlight Rust %}
match ordering {
    Ordering::Less =>  println!("less"),
    Ordering::Greater => println!("greater"),
    Ordering::Equal => println!("equal"),
}
{% endhighlight %}

Esta versão tem bem menos ruidos e conta com a vantagem de falhar em tempo de compilação quando adicionarmos mais uma opção no nosso enum.

Match também consegue recuperar valores de enuns:

{% highlight Rust %}
match character {
    Character::Digit(x) =>  println!("The digit is {}", x),
    Character::None => println!("None value selected"),
}
{% endhighlight %}

> match é uma expressão, o que significa que você pode usá-lo como retorno ou do lado exquerdo a `let`

## Looping

### For
Rust não tem o estilo padrão C:

{% highlight Rust %}
for (x = 0; x < 10; x++) {
    printf( "%d\n", x );
}
{% endhighlight %}

Rust segue um estilo de interator onde você define uma expressão que retorna um interator `0..10` e a variável que irá alocar o valor de cada interação. `x`:

{% highlight Rust %}
for x in 0..10 {
    println!("{}", x); // x: i32
}
{% endhighlight %}

### While
Outro tipo de looping é o `while`

{% highlight Rust %}
while !done {
    x += x - 3;
    println!("{}", x);
    if x % 5 == 0 { done = true; }
}
{% endhighlight %}

> Você tem disponível `break` e `continue` no while.

Existe ainda o `loop` para em casos onde o desenvolvedor vai definir quando deve sair do loop

{% highlight Rust %}
loop {
    x += x - 3;
    println!("{}", x);
    if x % 5 == 0 { break; }
}
{% endhighlight %}

## Strings
Rust tem dois tipos de strings  `&str` e `String`.

O primeiro, `&str`, são alocados de forma estática, possuem tamanho fixo e não pode ser alteradas.

{% highlight Rust %}
let string = "Hello there."; // string: &str
{% endhighlight %}


Por outro lado, `String`, são alocados na memória, possuem tamanho variável e podem ser alteradas.

{% highlight Rust %}
let mut s = "Hello".to_string(); // mut s: String
{% endhighlight %}

Você pode formar uma `String` a virar uma `&str` usando `&`.

{% highlight Rust %}
fn takes_slice(slice: &str) {
    println!("Got: {}", slice);
}

fn main() {
    let s = "Hello".to_string();
    takes_slice(&s);
}
{% endhighlight %}

Porém esta operação é cara pois envolve alocação de memória. **Não há porque fazer isto a menos que você realmente precise.**

Apenas lembre-se: `Strings` aloca memória e controla dados, enquanto `&strs` são referencias a outras strings.

## Arrays, Vectors, and Slices
Arrays em Rust tem tamanho fixo, de um mesmo tipo e por padrão imutáveis.
No exemplo abaixo. um array do tipo `i32` com 3 elementos.

{% highlight Rust %}
let a = [1, 2, 3];  // a: [i32; 3]
{% endhighlight %}

Você também pode inicializar um array usando `;` para definir o valor padrão e q quantidade de elementos:

{% highlight Rust %}
let a = [0; 20]; // a: [i32; 20]
{% endhighlight %}

Você também tem disponível funções como `len` e `iter`

{% highlight Rust %}
let a = [1, 2, 3];
for e in a.iter() {
    println!("{} of {}", e, a.len());
}
{% endhighlight %}

### Vector
Vetores são dinâmicos e como explica na documentação, `Vec<T>` são para arrays da mesma forma que `String` é para `&str`.

{% highlight Rust %}
let v = vec![1, 2, 3]; // v: Vec<i32>
{% endhighlight %}

Você tem as mesmas funções disponíveis em arrays com adicionais como `push` para adicionar elementos:

{% highlight Rust %}
v.push(4);
{% endhighlight %}

Podemos por exemplo usar `Slice` ou "fatia" de um array:

{% highlight Rust %}
let a = [0, 1, 2, 3, 4];
let middle = &a[1..4]; // A slice of a: just the elements 1, 2, and 3
for e in middle.iter() {
    println!("{}", e); // Prints 1, 2, 3
}
{% endhighlight %}


Como `&str` e `String` também são arrays, o mesmo pode ser feito com eles também.

## Standard Input

Nós podemos "pedir" ao usuário para digitar algo, da mesma forma como é feito em C# `Console.ReadLine()`. Para isso, precisaremos de importar um módulo (irei falar sobre modules mais a frente) `std::old_io;` ondem contém a função `old_io::stdin()`

{% highlight Rust %}
use std::old_io;
fn main() {
    println!("Type something!");
    let input = old_io::stdin().read_line().ok().expect("Failed to read line");
    println!("{}", input);
}
{% endhighlight %}


# Tests
Cargo por padrão cria novos projetos já com as classes de testes. Quando você executa `cargo new my-project`, uma classe de testes será criada com a seguinte estrutura:

    #[test]
    fn it_works() {
    }

Para rodar os testes basta executar: `cargo test`

Para transformar nossos testes em um modelo mais idiomático, você pode usar módulos e um atributo `cfg` para definir quando estes testes serão executados:

    #[cfg(test)]
    mod tests {
        // .. our tests here
    }

## Documentation
Este é com certeza uma dos recursos muito bacana do Rust, você consegue escrever documentação para seus módulos/métodos que sejam testáveis. Exemplo:

O código abaixo (com `//!`) define a documentação do módulo:

{% highlight Rust %}
//! The `test` crate provides functions that add numbers to other numbers.
//! # Examples
//! ```
//! assert_eq!(4, test::add_two(2));
//! ```
//! ```
//! assert_eq!(6, test::add_two(4));
//! ```
{% endhighlight %}


O código abaixo (com `///` barras), define a documentação da função;

{% highlight Rust %}
/// This function adds two to its argument.
///
/// # Examples
/// ```
/// use test::add_two;
/// assert_eq!(6, add_two(4));
/// ```
pub fn add_two(a: i32) -> i32 {
    a + 2
}
{% endhighlight %}

Ao executar os testes, rust compila e executa os exemplos:

## Bench
Bench são métricas de performance dos seus testes. Com Rust você precisa explicitamente executar estas métricas para saber quais estes estão lentos.

Para isso, adicione a anotação `#[bench]` no método:

{% highlight Rust %}
#[bench]
fn bench_add_two(b: &mut Bencher) {
    b.iter(|| add_two(2));
}
{% endhighlight %}

