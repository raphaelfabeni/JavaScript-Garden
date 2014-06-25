## `hasOwnProperty`

Para verificar se um objeto possui uma propriedade definida em si e não em 
algum lugar da [cadeia prototype](#object.prototype), é necessário utilizar o 
método `hasOwnProperty` que todos objetos herdam de `Object.prototype`.

> **Nota:** **Não** é suficiente verificar se uma propriedade é `undefined`. 
> A propriedade pode existir, mas seu valor é setado para `undefined`.

`hasOwnProperty` é a única coisa no JavaScript que trata de propriedades e que 
**não** percorre a cadeira *prototype*.

    // Poisoning Object.prototype
    Object.prototype.bar = 1;
    var foo = {goo: undefined};

    foo.bar; // 1
    'bar' in foo; // true

    foo.hasOwnProperty('bar'); // false
    foo.hasOwnProperty('goo'); // true

Só `hasOwnProperty` irá dar o correto e esperado resultado; isso é essencial 
quando ocorre iteração sobre as propriedades de qualquer objeto. **Não** há 
nenhuma outra maneira de excluir proriedades que não estão definidadas no 
próprio objeto, mas em algum lugar da cadeia *prototype*.

### `hasOwnProperty` como uma Propriedade

JavaScript não protege o nome da propriedade `hasOwnProperty`; assim, se a 
possibilidade existe de um objeto ter uma propriedade com esse nome, é 
necessário usar um `hasOwnProperty` *externo* para se obter resultados 
corretos.

    var foo = {
        hasOwnProperty: function() {
            return false;
        },
        bar: 'Here be dragons'
    };

    foo.hasOwnProperty('bar'); // sempre retorna false

    // Use outro objeto hasOwnProperty e chame-o com o 'this' definido para foo
    ({}).hasOwnProperty.call(foo, 'bar'); // true

    // Também é possível usar hasOwnProperty do Object prototype
    // para esse propósito
    Object.prototype.hasOwnProperty.call(foo, 'bar'); // true


### Conclusão

Usar `hasOwnProperty` é a **única** maneira confiável para verificar a 
existência de uma propriedade em um objeto. Recomenda-se que `hasOwnProperty` 
seja usado em **cada** [`for in` loop](#object.forinloop) para evitar erros 
de [prototypes](#object.prototype) nativos estendidos.