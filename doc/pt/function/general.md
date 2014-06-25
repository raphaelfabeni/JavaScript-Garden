## Declarações de Funções e Expressões

Funções no JavaScript são objetos de primeira classe. Isso significa que podem 
ser passados como qualquer outro valor. Um uso comum para essa funcionalidade 
é passar uma *função anônima* como *callback* para outra, possivelmente 
uma função assíncrona.

### A Declaração `function`

    function foo() {}

A função acima é [elevada (*hoisting*)](#function.scopes) antes da execução 
do programa iniciar; assim, ela está disponível *em todo lugar* no escopo que 
foi *definida*, mesmo se for chamada antes da sua definição no arquivo.

    foo(); // Funciona pois foo foi criada antes do código ser executado
    function foo() {}

### A Expressão `function`

    var foo = function() {};

Esse exemplo atribui uma função sem nome e *anônima* para a variável `foo`.

    foo; // 'undefined'
    foo(); // isso gera um TypeError
    var foo = function() {};

Devido ao fato de `var` ser uma declaração que eleva a variável `foo` antes 
da real execução do código iniciar, `foo` já está declarada quando o script 
é executado.

Mas desde que atribuições só acontecem durante a execução, o valor de `foo` 
será por padrão [undefined](#core.undefined), antes do código correspondente 
ser executado.

### Expressões de Funções Nomeadas

Outro caso especial é a atribuição de funções nomeadas.

    var foo = function bar() {
        bar(); // Funciona
    }
    bar(); // ReferenceError

Aqui, `bar` não está disponível no escopo externo, uma vez que a função só 
fica atribuida a `foo`; no entanto, dentro de `bar`, ela é disponível. 
Isso é devido a como a [resolução de nomes](#function.scopes) 
funciona no JavaScript, o nome da função é *sempre* disponível no escopo 
local da própria função.