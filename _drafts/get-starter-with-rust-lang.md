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

    rustc --version

## Introdução

Arquivos fontes do Rust sempre terminam com `.rs`, quando o arquivo tiver mais de uma palavra, recomenda-se usar `hello_world.rs` ao invés de `helloworld.rs`.

Você define uma função da seguinte forma:

    fn main() {

    }

A função `main` é o início de todo projeto Rust.
Você pode imprimir algo na tela usando o comando: `println!("Hello, world!");`. Recomenda-se uma edentação de 4 espaços (não tabs).

Observe o `!` ao final da função, o que diz ao Rust para executar a macro função (que é como o Rust executa metaprogramação).

## Compiler

Você pode usar `rustc` para compilar seu arquivo fonte e gerar um `helloworld.exe`. Rust irá gerar um arquivo já na sua plataforma (no meu caso windows).

## Cargo

Cargo é responsável por compilar seu projeto, gerenciar suas dependencias, e compilar suas dependencias.

Cargo utiliza um arquivo de configuração do [tipo TOML](https://github.com/toml-lang/toml) como `Cargo.toml` que define as propriedades do seu projeto:

    [package]

    name = "hello_world"
    version = "0.0.1"
    authors = [ "Your name <you@example.com>" ]

    [[bin]]

    name = "hello_world"

Para compilar seu projeto, simplesmente execute `cargo build`, isto irá gerar os executáveis na pasta `target`

## Variáveis

Variáveis são bem simples em Rust, veja este exemplo:

    let x: = 5;

Define uma variável `x` com valor 5; Como rust possui [type inference](http://en.wikipedia.org/wiki/Type_inference) ele irá descobrir qual i tipo da variável.
Porém podemos forçar o tipo:

    let x: i32 = 5;

Defta forma, definimos a variável `x` como do tipo `i32` com valor `5`.

Rust consegue trabalhar com [pattern matching](http://en.wikipedia.org/wiki/Pattern_matching), por exemplo, a expressão:

    let (x, y) = (1, 2);

irá atualizar a variável `x` com valor `1` e  `y` com valor `2`.

Por padrão variáveis são immutable, isto não irá compilar:

    let x = 5;
    x = 10;

Para conseguir trabalhar com estas variáveis, você pode usar `mut` apos o `let`:

    let mut x = 5; // mut x: i32
    x = 10;

isto irá compilar.

## Strings

Rust tem suporte a [String interpolation](http://en.wikipedia.org/wiki/String_interpolation), isto significa que você consegue fazer algo do tipo:

    println!("The value of x is: {}", x)

Rust irá substituir `{}` pelo `x`. Você pode inclusive definir um formato, existem vários [formatos disponívels na documentação](http://doc.rust-lang.org/std/fmt/)

## if

If em Rust é simples:

    let x = 5;
    if x == 5 {
        println!("x is five!");
    } else {
        println!("x is not five :(");
    }

então vou mostrar os diferenciais:

    let x = 5;
    let y = if x == 5 { 10 } else { 15 }; // y: i32

Rust trata o `if` como uma expressão, o que significa que ele retorna um valor.

> Isto mostra que Rust é uma linguagem baseada em expressão (expression-based language)

## Functions

    fn add_one(x: i32) -> i32 {
        x + 1
    }

Para funções Rust não possui tipos por inferência nos parâmetros, obrigado o programador a definir o tipo.
Repare que não existe `return` ou `;`, Rust utiliza a ultima expressão como retorno da função.

Você ainda pode utilziar `return`

    fn foo(x: i32) -> i32 {
        if x < 5 { return x; }
        return x + 1;
    }

porém este é um estilo mais pobre.

Como mencionado anteriormente, em Rust, `if` é uma expressão então nos permite fazer algo como:

## Comments

Rust tem suporte a dois tipos de comentários, line (usando `//`) e doc (usando `///`).

A principal diferença é que com doc comments você pode gerar uma documentação do seu código. Veja a documentação completa [neste link](http://doc.rust-lang.org/book/documentation.html).

## Compound Data Types

### Tuples

Tuples são listas ordenadas de tamanho fixo.

    let x = (1, "hello");
    let y: (i32, &str) = (1, "hello");

Os parenteses e a virgula definem o tamanho e os tipos;
Um truque bacana é que podemos usar Tuples como retorno de funções, permitindo uma função retornar mais de um valor:

    fn next_two(x: i32) -> (i32, i32) { (x + 1, x + 2) }

    fn main() {
        let (x, y) = next_two(5);
        println!("x, y = {}, {}", x, y);
    }


### Structs

Structs permite que você tenha *fields* nomeados.

    struct Point {
        x: i32,
        y: i32,
    }

    ...

    let origin = Point { x: 0, y: 0 }; // origin: Point
    println!("The origin is at ({}, {})", origin.x, origin.y);


By convention, structs usa capital letter e camel cased: Ou seja, use PointInSpace, ao invés de Point_In_Space.

### Enums

Enums são com em qualquer outra linguagem:

    enum Ordering {
        Less,
        Equal,
        Greater,
    }

e sua utilização é bem simples:

    let ordering = Ordering::Greater

    if ordering == Ordering::Less {
        println!("less");
    } else if ordering == Ordering::Greater {
        println!("greater");
    } else if ordering == Ordering::Equal {
        println!("equal");
    }

Porém com um pequeno e poderoso diferencial:

    enum Character {
        Digit(i32),
        Other,
    }

Observe o `Digit`, você pode definir o tipo de cada elemento.

## Match

Além do tradicional `if/else`, Rust conta com `Match`

    let x = 5;

    match x {
        1 => println!("one"),
        2 => println!("two"),
        3 => println!("three"),
        4 => println!("four"),
        5 => println!("five"),
        _ => println!("something else"),
    }

Além de deixar o codigo mais limpo, existe também um validador. Repare no `_`, ele representa o `else` do `if` porém no match ele é obrigatório.

Muito útil por exemplo com Enuns, lembra no nosso código?

    let ordering = Ordering::Greater

    if ordering == Ordering::Less {
        println!("less");
    } else if ordering == Ordering::Greater {
        println!("greater");
    } else if ordering == Ordering::Equal {
        println!("equal");
    }

Com match seria desta forma:

    match ordering {
        Ordering::Less =>  println!("less"),
        Ordering::Greater => println!("greater"),
        Ordering::Equal => println!("equal"),
    }

Esta versão tem bem menos ruidos e conta com a vantagem de falhar em tempo de compilação quando adicionarmos mais uma opção no nosso enum.

Match também consegue recuperar valores de enuns:

    match character {
        Character::Digit(x) =>  println!("The digit is {}", x),
        Character::None => println!("None value selected"),
    }

> match é uma expressão, o que significa que você pode usá-lo como retorno ou do lado exquerdo a `let`


## Looping

### For

Rust não tem o estilo padrão C:

    for (x = 0; x < 10; x++) {
        printf( "%d\n", x );
    }

Rust segue um estilo de interator onde você define uma expressão que retorna um interator `0..10` e a variável que irá alocar o valor de cada interação. `x`:

for x in 0..10 {
    println!("{}", x); // x: i32
}

### While

Outro tipo de looping é o `while`

while !done {
    x += x - 3;
    println!("{}", x);
    if x % 5 == 0 { done = true; }
}

> Você tem disponível `break` e `continue` no while.

Existe ainda o `loop` para em casos onde o desenvolvedor vai definir quando deve sair do loop

    loop {
        x += x - 3;
        println!("{}", x);
        if x % 5 == 0 { break; }
    }

## Strings

Rust tem dois tipos de strings  `&str` e `String`.

O primeiro, `&str`, são alocados de forma estática, possuem tamanho fixo e não pode ser alteradas.

    let string = "Hello there."; // string: &str

Por outro lado, `String`, são alocados na memória, possuem tamanho variável e podem ser alteradas.

    let mut s = "Hello".to_string(); // mut s: String

Você pode formar uma `String` a virar uma `&str` usando `&`.

    fn takes_slice(slice: &str) {
        println!("Got: {}", slice);
    }

    fn main() {
        let s = "Hello".to_string();
        takes_slice(&s);
    }

Porém esta operação é cara pois envolve alocação de memória. **Não há porque fazer isto a menos que você realmente precise.**

Apenas lembre-se: `Strings` aloca memória e controla dados, enquanto `&strs` são referencias a outras strings.

## Arrays, Vectors, and Slices

Arrays em Rust tem tamanho fixo, de um mesmo tipo e por padrão imutáveis.
No exemplo abaixo. um array do tipo `i32` com 3 elementos.

    let a = [1, 2, 3];  // a: [i32; 3]

Você também pode inicializar um array usando `;` para definir o valor padrão e q quantidade de elementos:

    let a = [0; 20]; // a: [i32; 20]

Você também tem disponível funções como `len` e `iter`

    let a = [1, 2, 3];
    for e in a.iter() {
        println!("{} of {}", e, a.len());
    }

### Vector

Vetores são dinâmicos e como explica na documentação, `Vec<T>` são para arrays da mesma forma que `String` é para `&str`.

    let v = vec![1, 2, 3]; // v: Vec<i32>

Você tem as mesmas funções disponíveis em arrays com adicionais como `push` para adicionar elementos:

    v.push(4);

Podemos por exemplo usar `Slice` ou "fatia" de um array:

    let a = [0, 1, 2, 3, 4];
    let middle = &a[1..4]; // A slice of a: just the elements 1, 2, and 3
    for e in middle.iter() {
        println!("{}", e); // Prints 1, 2, 3
    }

Como `&str` e `String` também são arrays, o mesmo pode ser feito com eles também.

## Standard Input

Nós podemos "pedir" ao usuário para digitar algo, da mesma forma como é feito em C# `Console.ReadLine()`. Para isso, precisaremos de importar um módulo (irei falar sobre modules mais a frente) `std::old_io;` ondem contém a função `old_io::stdin()`

    use std::old_io;
    fn main() {
        println!("Type something!");
        let input = old_io::stdin().read_line().ok().expect("Failed to read line");
        println!("{}", input);
    }


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

//! The `test` crate provides functions that add numbers to other numbers.
//! # Examples
//! ```
//! assert_eq!(4, test::add_two(2));
//! ```
//! ```
//! assert_eq!(6, test::add_two(4));
//! ```

O código abaixo (com `///` barras), define a documentação da função;

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

Ao executar os testes, rust compila e executa os exemplos:

## Bench

Bench são métricas de performance dos seus testes. Com Rust você precisa explicitamente executar estas métricas para saber quais estes estão lentos.

Para isso, adicione a anotação `#[bench]` no método:

    #[bench]
    fn bench_add_two(b: &mut Bencher) {
        b.iter(|| add_two(2));
    }
