    ## O Objeto `arguments`

Cada escopo de função no JavaScript pode acessar a variável especial
`arguments`. Essa variável contém uma lista de todos os argumentos que 
foram passados para a função.

> **Nota:** No caso de `arguments` já ter sido definido dentro do escopo da 
> função, sendo por uma declaração `var` ou sendo o nome de um parâmetro 
> formal, o objeto `arguments` não será criado.

O objeto `arguments` **não** é um array. Embora ele tenha algumas das 
semânticas de um array - ou seja, a propriedade `length` - não herda de 
`Array.prototype` e é de fato um objeto.

Devido a isso, **não** é possível usar métodos padrões de array como `push`, 
`pop` ou `slice` em `arguments`. Enquanto a iteração com um simples `for` 
funciona bem, é necessário convertê-lo para um `Array` real a fim de poder 
usar os métodos padrões de `Array`.

### Convertendo para um Array

O código abaixo irá retornar um novo `Array` contendo todos os elementos 
do objeto `arguments`.

    Array.prototype.slice.call(arguments);

Como essa conversão é **lenta**, **não** é recomendada usá-la em seções de 
código com desempenho crítico.

## Passando Argumentos

Abaixo é a maneira recomendada para passar agumentos de uma função para outra.

    function foo() {
        bar.apply(null, arguments);
    }
    function bar(a, b, c) {
        // faça coisas aqui
    }

Outra dica é usar ambos, `call` e `apply` juntos para criar `wrappers`
(invólucros) rápidos e desacoplados.

    function Foo() {}

    Foo.prototype.method = function(a, b, c) {
        console.log(this, a, b, c);
    };

    // Cria uma versão desacoplada de "method" 
    // Leva os parâmetros: this, arg1, arg2...argN
    Foo.method = function() {

        // Resultado: Foo.prototype.method.call(this, arg1, arg2... argN)
        Function.call.apply(Foo.prototype.method, arguments);
    };


### Parâmetros Formais e Índices de Argumentos

O objeto `arguments` cria funções *getter* e *setter* tanto suas propriedades, 
como para os parâmetros formais da função.

Como resultado, a alteração do valor de um parâmtro formal vai alterar também 
o valor da propriedade correspondente no objeto `arguments` e vice-versa.

    function foo(a, b, c) {
        arguments[0] = 2;
        a; // 2

        b = 4;
        arguments[1]; // 4

        var d = c;
        d = 9;
        c; // 3
    }
    foo(1, 2, 3);

### Mitos e Verdades sobre Perfomance

A única vez que o objeto `arguments` não é criado é quando ele é declarado 
como um nome dentro de uma função ou um de seus parâmetros formais. Assim, 
não importa se ele é usado ou não.

Ambos, *getters* e *setters* são **sempre** criados; assim, usar eles não 
impacta no desempenho no geral, especialmente no código do mundo real, 
onde há mais de um simples acesso às propriedades do objeto `arguments`.  

> **Nota ES5:** Esses *getters* e *setters* não são criados em `strict mode`.

No entanto, existe um caso que reduz drasticamente o desempenho de motores
JavaScript modernos. Esse caso é o uso do `arguments.callee`.

    function foo() {
        arguments.callee; // faça algo com essa função objeto
        arguments.callee.caller; // e a chamada da função objeto
    }

    function bigLoop() {
        for(var i = 0; i < 100000; i++) {
            foo(); // Normalmente será embutido
        }
    }

No código acima, `foo` já não pode ser um objeto para [`inlining`][1] uma vez 
que precisa conhecer tanto a si mesmo como seu `caller`. Isso só gera 
possíveis perda de desempenho que surgem a partir do `inlining`, mas também 
quebra o encapsulamento porque a função agora precisa ser dependente de um 
contexto de chamada específico.

Fazer uso de `arguments.callee` e quaisquer das suas propriedades é altamente 
desencorajado.

> **Nota ES5:** No `strict mode`, `arguments.callee` irá lançar um
> `TypeError` uma vez que seu uso se torno obsoleto.

[1]: http://en.wikipedia.org/wiki/Inlining


