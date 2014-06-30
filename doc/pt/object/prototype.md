## O Prototype

JavaScript não tem um modelo de herança clássica; em vez disso, utiliza
uma baseada no *prototype*.

Enquanto isso é muitas vezes considerado como um dos pontos fracos do
JavaScript, o modelo de herança `prototype` é de fato mais potente
que o o modelo clássico. Por exemplo, é relativamente simples
desenvolver um modelo clássico sob o modelo <i>prototype</i>, 
enquanto o contrário é uma tarefa muito mais difícil.

JavaScript é a única linguagem usada amplamente que apresenta herança pelo
<i>prototype</i>, por isso pode levar algum tempo para ajustar-se às
diferenças entre os dois modelos.

A primeira grande diferença é que herança em JavaScript utiliza *prototype
chains*.

> **Nota:** Simplesmente usando `Bar.prototype = Foo.prototype` resultará 
> em ambos objetos compartilhando o **mesmo** *prototype*. Portanto, 
> alterar o *prototype* de um objeto irá afetar o *prototype* do outro
> objeto também, o que na maioria dos casos não é o efeito desejado.

    function Foo() {
        this.value = 42;
    }
    Foo.prototype = {
        method: function() {}
    };

    function Bar() {}

    // Seta Bar prototype para uma nova instância Foo
    Bar.prototype = new Foo();
    Bar.prototype.foo = 'Hello World';

    // Certifique-se de listar Bar como construtor atual
    Bar.prototype.constructor = Bar;

    var test = new Bar(); // cria uma nova instância de bar

    // O resultado de prototype chain (cadeia de prototypes)
    test [instance of Bar]
        Bar.prototype [instance of Foo]
            { foo: 'Hello World' }
            Foo.prototype
                { method: ... }
                Object.prototype
                    { toString: ... /* etc. */ }

No código acima, o objeto `test` herdará de `Bar.prototype` e `Foo.prototype`;
portanto ele terá acesso à função `method` que foi definida em `Foo`. Ele
também tera acesso ao `value` da propriedade de **uma ** instância de `Foo`
que é o seu *prototype*. É importante notar que `new Bar()` **não** cria uma
nova instância `Foo`, mas reutiliza uma atribuída ao seu *prototype*; assim, 
todas instâncias `Bar` irão compartilhar a **mesma** propriedade `value`.

> **Nota:** **Não** use `Bar.prototype = Foo`, uma vez que não irá apontar para
> o *prototype* de `Foo` mas sim ao objeto função `Foo`. Assim, o
> a cadeia de *prototype* vai se sobrepor a `Function.prototype` e não 
> `Foo.prototype`; portanto, `method` não estará na cadeia de *prototype*.

### Procurando Propriedade

Ao acessar as propriedades de um objeto, JavaScript irá percorrer a cadeia do
*prototype* para cima até achar a propriedade com o nome solicitado.

Se atingir o topo da cadeia - nomeada `Object.prototype` - e ainda não tiver
encontrado a propriedade específica, ele irá retornar o valor
[undefined](#core.undefined) no lugar.

### A Propriedade Prototype

Enquanto a propriedade *prototype* é usada pela linguagem para construir a 
cadeira *prototype*, ainda é possível atribuir qualquer valor a ela. No entanto,
os primitivos serão simplemesmente ignorados quando atribuídos como
*prototype*.

    function Foo() {}
    Foo.prototype = 1; // sem efeito

Atribuir objetos, como mostrado no exemplo acima, irá funcionar e permite
a criação dinâmica de cadeias *prototype*.

### Performance

O tempo de pesquisa para as propriedades que estão no topo da cadeia 
*prototype* pode ter um impacto negativo na performance e, isso pode ser
significativo em códigos que o desempenho é crítico. Além disso, tentar 
acessar propriedades inexistentes irá sempre percorrer a cadeia 
*prototype* inteira.

Além disso, quando se [itera](#object.forinloop) sobre as propriedades 
de um objeto, **cada** propriedade que está na cadeia será enumerada.

### Extensão de *Prototypes* nativos

Uma característica inconveniente que é frequentemente usada é estender 
`Object.prototype` ou um dos outros contruídos em *prototype*.

Essa técnica é chamada [monkey patching][1] e quebra o *encapsulamento*. 
Enquanto é usada por frameworks populares como o [Prototype][2], ainda não há 
uma boa razão para sobrecarregar tipos internos (*built-in types*) com 
funcionalidades adicionais *não padrão*.

A **única** boa razão para estender um *prototype* interno é para transportar
as funcionalidades para novos motores JavaScript; por exemplo, [`Array.forEach`][3].

### Conclusão

É **essencial** entender o modelo de herança baseado em *prototype* antes de
escrever códigos complexos que fazem uso dela. Além disso, esteja ciente 
do tamanho das cadeias *prototype* no seu código e quebre elas, se 
necessário, para evitar possíveis problemas de desempenho. Outro ponto, os
*prototypes* nativos **nunca** devem ser estendidos a menos que seja por uma
questão de compatibilidade com novas funcionalidades JavaScript.

[1]: http://en.wikipedia.org/wiki/Monkey_patch
[2]: http://prototypejs.org/
[3]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/forEach

