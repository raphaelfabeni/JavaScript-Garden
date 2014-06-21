## Uso de objetos e propriedades

Tudo no JavaScript atua como um objeto, com apenas duas exceções sendo [`null`](#core.undefined) e [`undefined`](#core.undefined).

    false.toString(); // 'false'
    [1, 2, 3].toString(); // '1,2,3'
    
    function Foo(){}
    Foo.bar = 1;
    Foo.bar; // 1

Um equívoco comum é que números literais não podem ser usados como objetos. Isso porque uma falha no *parser* do JavaScript tenta reconhecer a notação de ponto em um número como um ponto flutuante literal.

    2.toString(); // gera um SyntaxError

Existem algumas maneiras que podem ser usadas para fazer números literais atuarem como objetos também.

    2..toString(); // o segundo ponto é reconhecido corretamente
    2 .toString(); // note o espaço à esquerda do pontonote the space left to the dot
    (2).toString(); // 2 é avaliado primeiro

### Objects como um tipo de dados (*Data Type*)

Objetos em JavaScript podem também ser usados com [*Hashmaps*][1]; eles consistem principalmente do mapeamento de propriedades e valores.

Usando um objeto literal - notação `{}` - é possível criar um objeto simple. Esse novo objeto [herda](#object.prototype) de `Object.prototype` e não tem [propriedades próprias](#object.hasownproperty) definidas.

    var foo = {}; // um novo objeto em branco

    // um novo objeto com uma propriedade 'test' com valor 12
    var bar = {test: 12}; 

### Acessando Propriedades

As propriedades de um objeto podem ser acessadas de duas maneiras, via pela notação ponto ou pela notação colchete.

    
    var foo = {name: 'kitten'}
    foo.name; // kitten
    foo['name']; // kitten
    
    var get = 'name';
    foo[get]; // kitten
    
    foo.1234; // SyntaxError
    foo['1234']; // works

As notações trabalham de forma quase idêntica, com a única diferença sendo que a notação de colchete permite a configuração dinâmica de propriedades e o uso de nomes de propriedades que, de outra forma, levariam a um erro de sintaxe.

### Excluindo Propriedades

A única maneira de remover uma propriedade de um objeto é usar o operador `delete`; setando a propriedade para `undefined` ou `null` apenas remove o *valor* associado à propriedade, mas não a *chave* (*key*).

    var obj = {
        bar: 1,
        foo: 2,
        baz: 3
    };
    obj.bar = undefined;
    obj.foo = null;
    delete obj.baz;

    for(var i in obj) {
        if (obj.hasOwnProperty(i)) {
            console.log(i, '' + obj[i]);
        }
    }

O código acima tem como resultado ambos: `bar undefined` e `foo null` - apenas `baz` foi removida e portanto está ausente do *output* do código.

### Notação de chaves

    var test = {
        'case': 'Eu sou uma keyword, portanto eu devo ser anotada como uma string',
        delete: 'Eu sou uma keyword, portanto eu também' // gera um SyntaxError
    };

Propriedades de objetos podem ser anotadas como caracteres simples e strings. Devido a outro *mis-design* no *parser* do JavaScrpt, o código acima irá mostrar um `SyntaxError`
antes do ECMAScript 5.

Este erro decorre do fato de que `delete` é uma *palavra-chave*; portanto, deve ser anotada como uma *string literal* para garantir que irá ser corretamente interpretada por motores JavaScript mais antigos.

[1]: http://en.wikipedia.org/wiki/Hashmap

