## Construtores 

Construtores em JavaScript são mais um recurso que difere de muitas outras 
linguagens. Qualquer chamada que é precedida pela palavra chave `new` 
atua como um construtor.

No interior de um construtor - função chamada - o valor de `this` se refere 
ao recém objeto criado. O [prototype](#object.prototype) desse **novo** 
objeto é definido como o `prototype` da função objeto que foi invocada 
como o construtor.

Se a função que foi chamada não tiver uma instrução `return` explícita, então 
implicitamente retorna o valor de `this` - o novo objeto.

    function Foo() {
        this.bla = 1;
    }

    Foo.prototype.test = function() {
        console.log(this.bla);
    };

    var test = new Foo();

No código acima `Foo` é chamada como um construtor e define o `prototype` 
do recém criado objeto para `Foo.prototype`.

No caso de uma declaração explícita de `return`, a função retorna o valor 
especificado por essa declaração, mas **somente** se o valor retornado 
for um `Object`.

    function Bar() {
        return 2;
    }
    new Bar(); // a new object

    function Test() {
        this.value = 2;

        return {
            foo: 1
        };
    }
    new Test(); // o objeto retornado

Quando a palavra chave `new` for omitida, a função **não** irá retornar um 
novo objeto.

    function Foo() {
        this.bla = 1; // fica definida no objeto global
    }
    Foo(); // undefined

Enquanto o exemplo acima pode funcionar em alguns casos, devido ao 
funcionamento do [`this`](#function.this) em JavaScript, ele usará o 
*objeto global* como valor de `this`.

### Factories

A fim de poder omitir a palavra chame `new`, a função construtora tem que 
retornar um valor explicitamente.

    function Bar() {
        var value = 1;
        return {
            method: function() {
                return value;
            }
        }
    }
    Bar.prototype = {
        foo: function() {}
    };

    new Bar();
    Bar();

Ambas as chamadas a `Bar` retornam a mesma coisa, o objeto recém criado que 
tem uma propriedade chamada `method`, a qual é uma 
[Closure](#function.closures).

Também deve ser notado que a chamada `new Bar()` **não** afeta o `prototype` 
do objeto retornado. Enquanto o `prototype` será definido no novo objeto 
criado, `Bar` nunca retornará aquele novo objeto.

No exemplo acima, não existe diferença funcional entre usar ou não usar a 
palavra chave `new`.

### Criando Novos Objetos via Factories

Recomenda-se **não** usar `new`, pois esquecer o seu uso pode levar a erros.

A fim de criar um novo objeto, deve-se preferir usar uma `factory` e 
construir um objeto no interior dessa `factory`.

    function Foo() {
        var obj = {};
        obj.value = 'blub';

        var private = 2;
        obj.someMethod = function(value) {
            this.value = value;
        }

        obj.getPrivate = function() {
            return private;
        }
        return obj;
    }

Enquanto o exemplo acima é robusto contra a falta da palavra chave `new` e 
certamente faz o uso de [variáveis privadas](#function.closures) facilmente, 
ele vem com algumas desvantagens.

 1. Usa mais memória, já que os objetos criados **não** compartilham os 
    métodos em um `prototype`.
 2. Em relação à herança, a `factory` precisa copiar todos os métodos de outro 
    objeto ou colocar esse objeto no `prototype` do novo objeto.
 3. Descartando a herança `prototype` apenas por causa de uma palavra
    chave `new` é contrário ao espírito da linguagem.

### Conclusão

Embora omitir a palavra chave `new` pode-se levar a erros, certamente 
**não** é uma razão para abandonar o uso dos `prototypes` completamente. No 
fim, tudo se resume a qual solução é mais adequada para as necessidades da
aplicação. É especialmente importante escolher um estilo específico de 
criação de objetos e utilizá-lo consistentemente.