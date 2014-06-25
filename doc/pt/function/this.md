## Como Funciona o `this`

JavaScript tem um conceito diferente sobre o que a palavra especial `this` se 
refere em relação à maioria das outras linguagens de programação. Existem 
exatamente **cinco** diferentes maneiras que o valor de `this` pode ser 
ligado à linguagem.

### O Escopo Global

    this;

Quando o `this` é usaodo no escopo global, ele vai simplesmente se referir 
ao objeto *global*.

### Chamando uma Função

    foo();

Aqui, `this` irá se referir novamente ao objeto *global*.

> **Nota ES5:** No *strict mode*, o processo global **não existe**. Nesse caso,
> `this` terá o valor `undefined`.

### Chamando um Método

    test.foo(); 

Nesse exemplo, `this` irá se referir a `test`.

### Chamando um Construtor

    new foo(); 

Uma chamada de função que é precedida pela keyword `new` atua como um 
[construtor](#function.constructors). Dentro da função, `this` irá se referir 
ao objeto *recém criado*.

### Definição Explícita do `this`

    function foo(a, b, c) {}
                          
    var bar = {};
    foo.apply(bar, [1, 2, 3]); // array se expandiá para baixo
    foo.call(bar, 1, 2, 3); // resulta em a = 1, b = 2, c = 3

Ao usar os métodos `call` ou `apply` de `Function.prototype`, o valor de 
`this` dentro da função chamada é definido explicitamente para o primeiro 
argumento função correspondente.

Como resultado, no exemplo acima, o *exemplo de método* **não** é aplicado e, 
`this` dentro `foo` será definido como `bar`. 

> **Nota:** `this` **não pode** ser usado para se referir ao objeto dentro de
> objeto literal. Portanto, `var obj = {me: this}` **não** irá resultar em
> `me` referindo-se a `obj`, uma vez que `this` só está vinculado a um dos 
> cinco casos listados.

### Problemas Comuns

Enquanto a maioria desses casos faz sentido, o primeiro pode ser considerado 
uma falha de design da linguagem porque **nunca** tem qualquer uso prático.

    Foo.method = function() {
        function test() {
            // this é definido ao objeto global
        }
        test();
    }

Um equívoco comum é que `this` dentro de `test` refere-se a `Foo`; o que de 
fato **não** acontece.

A fim de ter acesso a `Foo` dentro de `test`, é necessário criar uma 
variável local dentro de `method` que refere-se a `Foo`.

    Foo.method = function() {
        var that = this;
        function test() {
            // Use that ao invés de this aqui
        }
        test();
    }

`that` é apenas uma variável normal, mas é comumente usada para referenciar 
um `this`exterior. Em combinação com as [closures](#function.closures), pode 
também ser usado para passar valores de `this` para outras partes.

### Atribuição de Métodos

Outra coisa que **não** funciona no JavaScript é *function aliasing*, que é 
**atribuir** um método a uma variável.

    var test = someObject.methodTest;
    test();

Devido ao primeiro caso, `test` agora age como uma chamda de função simples; 
portanto, o `this` interior não se refere a `someObject`.

Enquanto a vinculação tardia de `this` pode parecer a princípio uma má idéia, 
na verdade, é o que faz a [herança prototype](#object.prototype) 
funcionar. 

    function Foo() {}
    Foo.prototype.method = function() {};

    function Bar() {}
    Bar.prototype = Foo.prototype;

    new Bar().method();

Quando `method` é chamado em uma instância de `Bar`, `this` irá se referir 
àquela exata instância.
