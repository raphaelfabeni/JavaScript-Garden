## *Closures* e Referências

Um dos recursos mais poderosos no JavaScript é a disponibilidade de *closures*.
Com closures, escopos **sempre** mantém acesso ao escopo externo, em que foram
definidos. Uma vez que que o único escopo que o JavaScript possuim é o 
[escopo de função](#function.scopes), todas funções, por padrão, atuam 
como closures.

### Emulando variáveis privadas

    function Counter(start) {
        var count = start;
        return {
            increment: function() {
                count++;
            },

            get: function() {
                return count;
            }
        }
    }

    var foo = Counter(4);
    foo.increment();
    foo.get(); // 5

Aqui, `Counter` retorna **duas** closures: a função `increment` e a função 
`get`. Ambas funções mantém a **referência** ao escopo de `Counter` e, 
por isso, sempre mantém acesso à variável `count` que foi definida naquele 
escopo.

### Porque Variáveis Privadas Funcionam

Como não é possível referenciar ou atribuir escopos em JavaScript, **não** 
existe maneira de acessar a variável `count` de fora. A única maneira de 
interagir com a variável é por meio das duas closures.

    var foo = new Counter(4);
    foo.hack = function() {
        count = 1337;
    };

O código acima **não** irá alterar a variável `count` no escopo `Counter`, 
uma vez que `foo.hack` não foi definido **naquele** escopo. Ao invés disso, 
será criada - ou sobreposta - a variável *global* `count`.

### Closures Dentro de Loops

Um erro frequente é usar closures no interior de loops, como se eles fossem 
copiar o valor da variável index do loop.

    for(var i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);  
        }, 1000);
    }

O código acima **não** irá mostrar os números de `0` até `9`, mas irá 
simplesmente imprimir o número `10` dez vezes.

A função *anônima* mantém **referência** a `i`. No momento que `console.log` 
é chamado, o `loop for` já terminou, e o valor de `i` foi definido para 10.

A fim de obter o comportamento desejado, é necessário criar uma **cópia** 
do valor de `i`.

### Evitando o Problema da Referência

Para copiar o valor da variável index do loop, é melhor usar um 
[*wrapper* anônimo](#function.scopes).

    for(var i = 0; i < 10; i++) {
        (function(e) {
            setTimeout(function() {
                console.log(e);  
            }, 1000);
        })(i);
    }

A função anônima externa é chamada imediatamente com `i` como seu primeiro 
argumento e receberá uma cópia do **valor** de `i` como seu parâmetro `e`.

A função anônima que é passada para `setTimeout` agora tem uma referência a 
`e`, cujo valor **não** é alterado pelo loop.

Existe uma outra maneira possível de atingir o mesmo resultado, que é 
retornar uma função do `wrapper`(invólucro) anônimo que terá então, o mesmo comportamento do código acima.

    for(var i = 0; i < 10; i++) {
        setTimeout((function(e) {
            return function() {
                console.log(e);
            }
        })(i), 1000)
    }

Há ainda uma outra maneira de chegar no mesmo resultado usando o `.bind`, 
que pode vincular um contexto e argumentos `this` para a função. 
O comportamento é o mesmo ao código acima.

    for(var i = 0; i < 10; i++) {
        setTimeout(console.log.bind(console, i), 1000);
    }
