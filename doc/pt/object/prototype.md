## O Prototype

JavaScript não tem um modelo de herança clássica; em vez disso, utiliza uma baseada no *prototype*.

Enquanto isso é muitas vezes considerado como um dos pontos fracos do JavaScript, o modelo de herança de protótipo é de fato mais potente que o o modelo clássico. Por exemplo, é relativamente simples desenvolver um modelo clássico sob o modelo <i>prototype</i>, enquanto o contrário é uma tarefa muito mais difícil.

JavaScript é a única linguagem usada amplamente que apresenta herança pelo <i>prototype</i>, por isso pode levar algum tempo para ajustar-se às diferenças entre os dois modelos.

A primeira grande diferença é que herança em JavaScript utiliza *prototype chains*.

> **Nota:** Simplesmente usando `Bar.prototype = Foo.prototype` resultará em ambos objetos
> compartilhamento o **mesmo** *prototype*. Portanto, alterar o *prototype* de um objeto
> irá afetar o *prototype* do outro objeto também, o que na maioria dos casos não é o
> efeito desejado.

    function Foo() {
        this.value = 42;
    }
    Foo.prototype = {
        method: function() {}
    };

    function Bar() {}

    // Seta o Bar prototype para uma nova instância Foo
    Bar.prototype = new Foo();
    Bar.prototype.foo = 'Hello World';

    // Certifique-se de listar Bar como construtor atual
    Bar.prototype.constructor = Bar;

    var test = new Bar(); // cria uma nova instância de bar

    // O resultado de prototype chain
    test [instance of Bar]
        Bar.prototype [instance of Foo]
            { foo: 'Hello World' }
            Foo.prototype
                { method: ... }
                Object.prototype
                    { toString: ... /* etc. */ }

No código acima, o objeto `test` herdará de `Bar.prototype` e `Foo.prototype`; portanto ele terá acesso à função `method` que foi definida em `Foo`. Ele também tera acesso ao `value` da propriedade de **uma ** instância de `Foo` que é o seu *prototype*. É importante notar que a `new Bar()` **não** cria uma nova instância `Foo`, mas reutiliza uma atribuída ao seu *prototype*; assim, todas instâncias `Bar` irão compartilhar a **mesma** propriedade `value`.

> **Note:** Do **not** use `Bar.prototype = Foo`, since it will not point to
> the prototype of `Foo` but rather to the function object `Foo`. So the
> prototype chain will go over `Function.prototype` and not `Foo.prototype`;
> therefore, `method` will not be on the prototype chain.

### Property Lookup

When accessing the properties of an object, JavaScript will traverse the
prototype chain **upwards** until it finds a property with the requested name.

If it reaches the top of the chain - namely `Object.prototype` - and still
hasn't found the specified property, it will return the value
[undefined](#core.undefined) instead.

### The Prototype Property

While the prototype property is used by the language to build the prototype
chains, it is still possible to assign **any** given value to it. However,
primitives will simply get ignored when assigned as a prototype.

    function Foo() {}
    Foo.prototype = 1; // no effect

Assigning objects, as shown in the example above, will work, and allows for dynamic
creation of prototype chains.

### Performance

The lookup time for properties that are high up on the prototype chain can have
a negative impact on performance, and this may be significant in code where
performance is critical. Additionally, trying to access non-existent properties
will always traverse the full prototype chain.

Also, when [iterating](#object.forinloop) over the properties of an object
**every** property that is on the prototype chain will be enumerated.

### Extension of Native Prototypes

One mis-feature that is often used is to extend `Object.prototype` or one of the
other built in prototypes.

This technique is called [monkey patching][1] and breaks *encapsulation*. While
used by popular frameworks such as [Prototype][2], there is still no good
reason for cluttering built-in types with additional *non-standard* functionality.

The **only** good reason for extending a built-in prototype is to backport
the features of newer JavaScript engines; for example,
[`Array.forEach`][3].

### In Conclusion

It is **essential** to understand the prototypal inheritance model before
writing complex code that makes use of it. Also, be aware of the length of the
prototype chains in your code and break them up if necessary to avoid possible
performance problems. Further, the native prototypes should **never** be
extended unless it is for the sake of compatibility with newer JavaScript
features.

[1]: http://en.wikipedia.org/wiki/Monkey_patch
[2]: http://prototypejs.org/
[3]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/forEach

