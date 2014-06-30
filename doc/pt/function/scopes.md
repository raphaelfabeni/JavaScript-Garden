## Escopos e Namespaces

Embora JavaScript lide bem com a sintaxe de duas chaves para blocos, ela 
**não** suporta escopo em bloco; portanto, tudo que resta na linguagem é 
*escopo de função*.

    function test() { // a scope
        for(var i = 0; i < 10; i++) { // não é escopo
            // count
        }
        console.log(i); // 10
    }

> **Nota:* Quando não for utilizado em uma atribuição, uma instrução `return` 
> ou como um argumento de função, a notação `{...}` vai ser interpretada como 
> uma declaração de bloco e **não** como um objeto literal. Isso em conjunto, 
> com a [inserção automática de ponto e vírgula](#core.semicolon), pode 
> levar a erros sutis.

Também não há *namespaces* distintos em JavaScript, o que significa que tudo 
é definido em um *namespace global* compartilhado.

Cada vez que uma variável é referenciada, JavaScript irá percorrer em todos os 
escopos até achá-la. No caso que o âmbito global for atingido e a variável não 
tiver sido encontrada, isso resultará em um `ReferenceError`.

### A Ruína de Variáveis Globais

    // script A
    foo = '42';

    // script B
    var foo = '42'

Os dois scripts acima **não** tem o mesmo efeito. o script A define uma 
variável chamada `foo` no escopo *global*, e o script B define uma variável 
`foo` no escopo *atual*.

Mais uma vez, não é relacioado ao *mesmo efeito*: não usar `var` pode ter 
grandes implicações.

    // global scope
    var foo = 42;
    function test() {
        // escopo local
        foo = 21;
    }
    test();
    foo; // 21

Não inserindo a declaração `var` dentro da função `test` irá substituirá o 
valor de `foo`. Enquanto isso pode não parecer um grande problema em um 
primeiro momento, ter milhares de linhas de JavaScript e não usar `var` 
vai lhe introduzir bugs horríveis e difíceis de encontrar.
    
    // global scope
    var items = [/* some list */];
    for(var i = 0; i < 10; i++) {
        subLoop();
    }

    function subLoop() {
        // scope of subLoop
        for(i = 0; i < 10; i++) { // faltando declaração var
            // do amazing stuff!
        }
    }

O loop externo irá terminar depois da primeira chamada a `subloop`, uma vez 
que `subloop` substitui o valor global de `i`. Usando um `var` para o segundo 
loop `for` esse erro seria facilmente evitado. A declaração `var` **nunca** 
deve ser deixada de fora a menos que o *efeito desejado* seja afetar o 
escopo externo.

### Variáveis Locais

A única fonte de variáveis locais em JavaScript são parâmetros de 
[funções](#function.general) e variáveis declaradas por meio da declaração 
`var`.

    // global scope
    var foo = 1;
    var bar = 2;
    var i = 2;

    function test(i) {
        // escopo local da função test
        i = 5;

        var foo = 3;
        bar = 4;
    }
    test(10);

Enquanto `foo` e `i` são variáveis locais dentro do escopo da função `test`, 
a atribuição de `bar` irá substituir a variável global com o mesmo nome.

### Hoisting

JavaScript **eleva** declarações. Isso significa que ambos, declarações `var`  
e declarações `function` serão movidos para o topo do seu escopo.

    bar();
    var bar = function() {};
    var someValue = 42;

    test();
    function test(data) {
        if (false) {
            goo = 1;

        } else {
            var goo = 2;
        }
        for(var i = 0; i < 100; i++) {
            var e = data[i];
        }
    }

O código acima é transformado antes do inicio da sua execução. JavaScript 
move as declarações `var`, assim como as declarações `function`, para o 
topo do escopo mais próximo.

    // declarações var são movidas aqui
    var bar, someValue; // default para 'undefined'

    // as declarações de função também são movidas
    function test(data) {
        var goo, i, e; // escopo de bloco ausente move essas variáveis p/ cá
        if (false) {
            goo = 1;

        } else {
            goo = 2;
        }
        for(i = 0; i < 100; i++) {
            e = data[i];
        }
    }

    bar(); // falha com um TypeError uma vez que bar continua 'undefined'
    someValue = 42; // atribuições não são afetadas pelo hoisting
    bar = function() {};

    test();

Escopos de bloco ausentes não só moverão as declarações `var` para fora de 
seus loops e corpos, mas também farão os resultados de certos construtores 
`if` não intuitivos.

No código original, embora a declaração `if` pareça modificar a *variável 
global* `goo`, na verdade ela modifica a *variável local* - depois da elevação 
(*hoisting*) ser aplicada.

Sem o conhecimento de elevação (*hoisting*), pode-se suspeitar que o código 
abaixo retornaria um `ReferenceError`.

    // verifica se SomeImportantThing foi inicializada
    if (!SomeImportantThing) {
        var SomeImportantThing = {};
    }

Mas claro, que isso funciona devido ao fato da declaração `var` ser movida 
para o topo do *escopo global*.

    var SomeImportantThing;

    // outro código pode ou não inicializar SomeImportantThing aqui

    // tenha certeza de que está lá
    if (!SomeImportantThing) {
        SomeImportantThing = {};
    }

### Ordem da Resolução de Nomes

Todos escopos em JavaScript, incluindo o *escopo global*, tem o nome
especial [`this`](#function.this), definidos em si, que se refere ao *objeto 
atual*.

Escopos de função também tem o nome [`arguments`](#function.arguments), 
definido em si, e que contém os argumentos que foram passados para a função.

Por exemplo, ao tentar acessar uma variável de nome `foo` dentro do escopo de 
uma função, JavaScript irá procurar pelo nome na seguinte ordem:

 1. No caso de haver uma declaração `var foo` dentro do escopo atual, use ela.
 2. Se um dos parâmetros da função é nomeado `foo`, use ele.
 3. Se a própria função é chamada `foo`, use ela.
 4. Vá para o escopo seguinte e, comece **#1** de novo.

> **Nota:** Ter um parâmetro chamado `arguments` evita a criação do objeto 
> padrão `arguments`.

### Namespaces

Um problema comum associado a se ter apenas um *namespace* global é a 
probabilidade de nomes de variáveis se coincidirem. Em JavaScript esse 
problema pode ser facilmente evitado com a ajuda dos *wrappers anônimos*.

    (function() {
        // um "namespace" reservado
        
        window.foo = function() {
            // uma closure exposta
        };

    })(); // executa a função imediatamente

Função sem nome são consideradas [expressões](#function.general); assim, a fim 
de serem acessíveis, elas precisam primeiro serem avaliadas.

    ( // avalia a função dentro dos parênteses
    function() {}
    ) // e retorna o objeto da função
    () // chama o resultado da avaliação

Há outras maneiras de avaliar e chamar expressões de função, que embora 
difiram na sintaxe, se comportam da mesma maneira.

    // Alguns outros estilos para invocar diretamente
    !function(){}()
    +function(){}()
    (function(){}());
    // e assim por diante...

### Conclusão

Recomenda-se sempre usar um *wrapper anônimo* para encapsular o código em seu 
próprio namespace. Isso não só protege contra os conflitos de nomes, mas 
também permite uma melhor modularização dos programas.

Adicionalmente, o uso de variáveis globais é considerado uma **prática ruim**. 
**Qualquer** uso delas indica código mal escrito que está propenso a erros 
e difícil de manter.

