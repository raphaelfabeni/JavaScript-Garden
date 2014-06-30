## O Loop `for in`

Assim como no operador `in`, o loop `for in` percorre a cadeia *prototype* 
quando itera sobre as propriedades de um objeto.

> **Nota:** O loop `for in` **não** irá iterar sobre quaisquer propriedades 
> que têm seus atributos `enumeráveis` definidos como `false`; por exemplo, a 
> propriedade `length` de um array.
    
    // Poisoning Object.prototype
    Object.prototype.bar = 1;

    var foo = {moo: 2};
    for(var i in foo) {
        console.log(i); // resulta em ambos: bar e moo
    }

Uma vez que não é possível alterar o comportamento do loop `for in` em si, 
é necessário filtrar as propriedades indesejadas dentro do loop; isso é feito 
usando o método [`hasOwnProperty`](#object.hasownproperty) do 
`Object.prototype`.

> **Nota:** Como o `for in` percorre a cadeia *prototype* completa, cada 
> camada de herança adiciona ao objeto, deixará a execução mais lenta.

### Usando `hasOwnProperty` para Filtragem

    // o mesmo foo acima
    for(var i in foo) {
        if (foo.hasOwnProperty(i)) {
            console.log(i);
        }
    }

Essa versão é a única correta a se usar. Devido ao uso do `hasOwnProperty`, 
**apenas** será impresso `moo`. Quando `hasOwnProperty` não é utilizado, o 
código é propenso a erros em casos onde *prototypes* nativos - e.g. 
`Object.prototype` - são estendidos.

Um framework muito utilizado que estende o `Object.prototype` é o 
[Prototype][1]. Quando esse framework está incluído, os loops `for in` que 
não usam `hasOwnProperty` estão garantidos a quebrar.

### Conclusão

Recomenda-se **sempre** usar `hasOwnProperty`. Nunca devem ser feitos 
pressupostos sobre o ambiente em que o código está sendo executado, ou se 
os *prototypes* nativos foram estendidos ou não.

[1]: http://www.prototypejs.org/

