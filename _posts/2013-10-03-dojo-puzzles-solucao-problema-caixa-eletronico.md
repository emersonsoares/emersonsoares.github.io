---
layout:     post
title:      Dojo Puzzles - Solução para o problema do Caixa Eletrônico
date:       2013-10-03 00:59:32
summary:    Resolução para o problema do Caixa Eletrônico
categories: algoritmos
permalink: /dojo-puzzles-solucao-problema-caixa-eletronico/
---

De vez em quando me pego brincando com um ou outro problema lá do [Dojo Puzzles](http://dojopuzzles.com/), e resolvi criar um [repositório no Github](https://github.com/emersonsoares/DojoPuzzlesSolutions) e começar a compartilhar minhas soluções.
A primeira delas é a resolução para o problema do [Caixa Eletrônico](http://dojopuzzles.com/problemas/exibe/caixa-eletronico/). 

Segue abaixo o enunciado:

Desenvolva um programa que simule a entrega de notas quando um cliente efetuar um saque em um caixa eletrônico.
Os requisitos básicos são os seguintes:

- Entregar o menor número de notas;
- É possível sacar o valor solicitado com as notas disponíveis;
- Saldo do cliente infinito;
- Quantidade de notas infinito;
- Notas disponíveis de** $ 100,00**;** $ 50,00**; **$ 20,00** e **$ 10,00;**

**Casos de teste:**

- Um saque de $30,00 deve retornar: 1 nota de $ 20,00; 1 nota de $10,00;
- Um saque de $80,00 deve retornar: 1 nota de $50,00; 1 nota de $20,00; 1 nota de $10,00;
- Um saque de $180,00 deve retornar: 1 nota de $100,00; 1 nota de $50,00; 1 nota de $20,00; 1 nota de $10,00;

Vou trabalhar com dois objetos: CashMachine(Caixa eletrônico) e Withdraw(Saque). O objeto Withdraw representará um saque, provendo então o numero de cédulas de $100,00, de $50,00, de $20,00 e de $10,00 pertencentes ao respectivo saque. O caixa eletrônico tem a responsabilidade de retornar um saque, considerando as melhores opções para distribuição das cédulas.

**Testes**

Aqui basicamente vamos testar as propriedades do Saque, elas devem retornar a quantidade correta de cada nota contida no saque:

```csharp
[TestClass]
public class WithdrawTests
{
    [TestMethod]
    public void TestReturnOfNotes()
    {
        var withdraw = new Withdraw(1, 1, 1, 1);
 
        Assert.AreEqual(1, withdraw.TenNotes);
        Assert.AreEqual(1, withdraw.TwentyNotes);
        Assert.AreEqual(1, withdraw.FiftyNotes);
        Assert.AreEqual(1, withdraw.HundredNotes);
    }
}
```

E por fim os testes para o Caixa Eletrônico, representei todos os casos de teste do enunciado do problema:

```csharp
namespace CashMachine.Tests
{
    [TestClass]
    public class CashMachineTests
    {
        [TestMethod]
        public void WithdrawOfThirtyShouldBeReturnOneTenNoteAndTwoTwentyNotes()
        {
            var cashMachine = CashMachine.New();
            var withdraw = cashMachine.Get(30);
 
            var expectedWithdraw = new Withdraw(1, 1, 0, 0);
 
            Assert.AreEqual(expectedWithdraw, withdraw);
        }
 
        [TestMethod]
        public void WithdrawOfEightyShouldBeReturnOneTenNoteAndOneTwentyNotesAndOneFiftyNote()
        {
            var cashMachine = CashMachine.New();
            var withdraw = cashMachine.Get(80);
 
            var expectedWithdraw = new Withdraw(1, 1, 1, 0);
 
            Assert.AreEqual(expectedWithdraw, withdraw);
        }
 
        [TestMethod]
        public void WithdrawOfOneHundredEightyShouldBeReturnOneTenNoteAndOneTwentyNotesAndOneFiftyNoteAndOneHundredNote()
        {
            var cashMachine = CashMachine.New();
            var withdraw = cashMachine.Get(180);
 
            var expectedWithdraw = new Withdraw(1, 1, 1, 1);
 
            Assert.AreEqual(expectedWithdraw, withdraw);
        }
    }
}
```

**Implementação**

Implementação do Saque:

```csharp
namespace CashMachine
{
    public struct Withdraw
    {
        private readonly int _tenNotes;
        private readonly int _twentyNotes;
        private readonly int _fiftyNotes;
        private readonly int _hundredNotes;
 
        public Withdraw(int tenNotes, int twentyNotes, int fiftyNotes, int hundredNotes)
        {
            _tenNotes = tenNotes;
            _twentyNotes = twentyNotes;
            _fiftyNotes = fiftyNotes;
            _hundredNotes = hundredNotes;
        }
 
        public int TenNotes { get { return _tenNotes; } }
 
        public int TwentyNotes { get { return _twentyNotes; } }
 
        public int FiftyNotes { get { return _fiftyNotes; } }
 
        public int HundredNotes { get { return _hundredNotes; } }
    }
}
```

Implementação do Caixa Eletrônico:

```csharp
namespace CashMachine
{
    public class CashMachine
    {
        private CashMachine()
        { }
 
        public static CashMachine New()
        {
            return new CashMachine();
        }
 
        public Withdraw Get(int value)
        {
            int hundredRest;
            var hundredNotes = Math.DivRem(value, 100, out hundredRest);
 
            int fiftyRest;
            var fiftyNotes = Math.DivRem(hundredRest, 50, out fiftyRest);
 
            int twentyRest;
            var twentyNotes = Math.DivRem(fiftyRest, 20, out twentyRest);
 
            int tenRest;
            var tenNotes = Math.DivRem(twentyRest, 10, out tenRest);
 
            return new Withdraw(tenNotes, twentyNotes, fiftyNotes, hundredNotes);
        }
    }
}
```

Como podemos ver, implementei uma struct que representa o Saque, que retorna o numero de notas correspondente para cada cédula do saque. Essa struct não contém nenhuma regra para o Saque, pois, caso o nosso caixa eletrônico esteja bugado ele pode retornar um saque que não obedece as regras do enunciado. Por isso então deixei que o Caixa Eletrônico fizesse o calculo das melhores opções de notas e retornasse um objeto Saque.

Bem, essa foi minha solução para o problema do Caixa Eletrônico, talvez com uma analise mais detalhada isso poderia ser melhorado.

Aceito sugestões e até o próximo problema :)