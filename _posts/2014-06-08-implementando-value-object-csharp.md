---
layout:     post
title:      Implementando um value object em CSharp
date:       2014-06-08 11:21:29
summary:    This is an empty post to illustrate the pagination component with Pixyll.
categories: domain driven design
permalink: /implementando-value-object-csharp/
---

Bom pessoal neste post vou tentar explicar um pouco sobre Value Objects ou Objetos de Valor e demonstrar a implementação de um objeto de valor em CSharp.

>Muitos objetos não possuem nenhuma identidade conceitual. Esses objetos descrevem alguma característica de alguma coisa. - Eric Evans

Ainda citando Eric Evans no livro Domain Driven Design, 

>Quando uma criança está desenhando, ela está preocupada com a cor da caneta que vai escolher, e pode estar preocupada com a espessura da ponta. Mas se houver duas canetas da mesma cor e de mesmo formato, ela provavelmente não vai se importar em qual delas está utilizando. Se uma caneta se perder e for substituída por outra da mesma cor tirada de uma nova embalagem, ela pode recomeçar o seu trabalho sem se preocupar com a troca.

Objetos de valor representam um aspecto descritivo do domínio, não sendo necessária nenhuma identidade conceitual. Uma instancia de um objeto de valor que tenha as mesmas características de uma segunda instância do mesmo objeto não devem ser diferenciadas por identidades únicas, pois, afinal o que importa são suas características. Um exemplo simples de objetos de valor, são as cores, não importa onde você utilize uma instancia de Vermelho em seu domínio, o vermelho será sempre vermelho, diferente de uma entidade onde, por mais que tenham características parecidas, sua identidade é diferente e importa para o domínio.

Vou utilizar um case da vida real para tentar explicar melhor. Em um software que estou trabalhando atualmente, nosso domínio tem por exemplo, **Terrenos** e **Edificações** como entidades. Todo Terreno tem uma **Area**, assim como toda Edificação. Em alguns módulos do sistema precisamos utilizar um Terreno com uma **Area** em **Hectares**, já a Edificação em **Metros Quadrados**. Ou em alguns casos mais específicos alguns Terrenos tem sua **Area** especificada em Alqueires ou Acres. O fato é que o sistema precisa fazer cálculos entre Áreas de Terrenos e Edificações, em alguns lugares precisaremos da Área de um Terreno em metros quadrados, em outros precisaremos da área em Hectares ou Alqueires.

Para resolver esse problema, criamos um Tipo chamado Area e esse tipo implementa todas as características de um Objeto de Valor. Então imaginemos o seguinte, um Terreno que tem uma Area especificada em metros quadrados e um segundo terreno que tem uma Area especificada em Hectares.

**Terreno1 = 10.000m²**
**Terreno2 = 1hectare**

O nosso design deve permitir a comparação entre as áreas desses terrenos, e mais, para o sistema a área dos dois terrenos deve ser a mesma, pois 1 hectare corresponde exatamente a 10.000 metros quadrados. Sendo assim temos aqui a configuração de um objeto de valor.

Primeiro, vejamos os testes necessários para o nosso objeto de valor **Area**:

```csharp
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace DDDLibrary.Tests
{
    [TestClass]
    public class AreaTests
    {
        [TestMethod]
        public void AreaInSquareMetersMustBeReturnSquareMeters()
        {
            var area = Area.FromSquareMeters(1000);
            const int expected = 1000;

            Assert.AreEqual(expected, area.InSquareMeters());
        }

        [TestMethod]
        public void AreaWithIdenticSquareMetersEqualsMustBeReturnTrue()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(1000);

            Assert.IsTrue(area1.Equals(area2));
        }

        [TestMethod]
        public void AreaWithDiferentSquareMetersEqualsMustBeReturnFalse()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(2000);

            Assert.IsFalse(area1.Equals(area2));
        }

        [TestMethod]
        public void AreaWithIdenticSquareMetersEqualsOperatorMustBeReturnTrue()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(1000);

            Assert.IsTrue(area1 == area2);
        }

        [TestMethod]
        public void AreaWithDiferentSquareMetersEqualsOperatorMustBeReturnTrue()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(2000);

            Assert.IsFalse(area1 == area2);
        }

        [TestMethod]
        public void AreaWithIdenticSquareMetersDiferentOperatorMustBeReturnFalse()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(1000);

            Assert.IsFalse(area1 != area2);
        }

        [TestMethod]
        public void AreaWithDiferentSquareMetersDiferentOperatorMustBeReturnTrue()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(2000);

            Assert.IsTrue(area1 != area2);
        }

        [TestMethod]
        public void AreaComparationWithADiferentObjectEqualsMustBeReturnFalse()
        {
            var area = Area.FromSquareMeters(1000);
            var obj = new Object();

            Assert.IsFalse(area.Equals(obj));
        }

        [TestMethod]
        public void AreaComparationWithAObjectOfTypeAreaMustBeReturnTrue()
        {
            var area = Area.FromSquareMeters(1000);
            object area2 = Area.FromSquareMeters(1000);

            Assert.IsTrue(area.Equals(area2));
        }

        [TestMethod]
        public void HashCodeOfTwoAreasWithEqualSquareMetersMustBeTheSame()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(1000);

            Assert.AreEqual(area1.GetHashCode(), area2.GetHashCode());
        }

        [TestMethod]
        public void HashCodeOfTwoAreasWithDiferentSquareMetersMustBeDiferent()
        {
            var area1 = Area.FromSquareMeters(1000);
            var area2 = Area.FromSquareMeters(2000);

            Assert.AreNotEqual(area1.GetHashCode(), area2.GetHashCode());
        }

        [TestMethod]
        public void AreaFromHectaresMustBeReturnAnAreaWithValidSquareMeters()
        {
            var area = Area.FromHectares(1);
            var area2 = Area.FromSquareMeters(10000);

            Assert.AreEqual(area, area2);
        }

        [TestMethod]
        public void AreaFromAcresMustBeReturnAnAreaWithValidSquareMeters()
        {
            var area = Area.FromAcres(1);
            var area2 = Area.FromSquareMeters(4046.856422m);

            Assert.AreEqual(area, area2);
        }

        [TestMethod]
        public void AreaFromAlqueiresDoNorteMustBeReturnAnAreaWithValidSquareMeters()
        {
            var area = Area.FromAlqueiresDoNorte(1);
            var area2 = Area.FromSquareMeters(27000.225m);

            Assert.AreEqual(area, area2);
        }

        [TestMethod]
        public void AreaFromAlqueiresPaulistaMustBeReturnAnAreaWithValidSquareMeters()
        {
            var area = Area.FromAlqueiresPaulista(1);
            var area2 = Area.FromSquareMeters(24000.200m);

            Assert.AreEqual(area, area2);
        }

        [TestMethod]
        public void AreaFromAlqueiresMineiroMustBeReturnAnAreaWithValidSquareMeters()
        {
            var area = Area.FromAlqueiresMineiro(1);
            var area2 = Area.FromSquareMeters(48000.400m);

            Assert.AreEqual(area, area2);
        }

        [TestMethod]
        public void AreaInHectaresMustBeReturnHectaresOfArea()
        {
            var area = Area.FromSquareMeters(10000);
            var area2 = Area.FromHectares(2);

            Assert.AreEqual(1, area.InHectares());
            Assert.AreEqual(2, area2.InHectares());
        }

        [TestMethod]
        public void AreaInAcresMustBeReturnAcresOfArea()
        {
            var area = Area.FromSquareMeters(4046.856422m);
            var area2 = Area.FromAcres(2);

            Assert.AreEqual(1, area.InAcres());
            Assert.AreEqual(2, area2.InAcres());
        }

        [TestMethod]
        public void AreaInAlqueiresDoNorteMustBeReturnAlqueiresDoNorteOfArea()
        {
            var area = Area.FromSquareMeters(27000.225m);
            var area2 = Area.FromAlqueiresDoNorte(2);

            Assert.AreEqual(1, area.InAlqueiresDoNorte());
            Assert.AreEqual(2, area2.InAlqueiresDoNorte());
        }

        [TestMethod]
        public void AreaInAlqueiresPaulistaMustBeReturnAlqueiresPaulistaOfArea()
        {
            var area = Area.FromSquareMeters(24000.200m);
            var area2 = Area.FromAlqueiresPaulista(2);

            Assert.AreEqual(1, area.InAlqueiresPaulista());
            Assert.AreEqual(2, area2.InAlqueiresPaulista());
        }

        [TestMethod]
        public void AreaInAlqueiresMineiroMustBeReturnAlqueiresMineiroOfArea()
        {
            var area = Area.FromSquareMeters(48000.400m);
            var area2 = Area.FromAlqueiresMineiro(2);

            Assert.AreEqual(1, area.InAlqueiresMineiro());
            Assert.AreEqual(2, area2.InAlqueiresMineiro());
        }

        [TestMethod]
        public void MustBePossibleSumTwoAreas()
        {
            var area = Area.FromSquareMeters(10);
            var area2 = Area.FromSquareMeters(10);

            var expected = Area.FromSquareMeters(20);

            Assert.AreEqual(expected, area + area2);
        }

        [TestMethod]
        public void MustBePossibleSumAnAreaWithSquareMeters()
        {
            var area = Area.FromSquareMeters(10);

            var expected = Area.FromSquareMeters(20);

            Assert.AreEqual(expected, area + 10);
        }

        [TestMethod]
        public void MustBePossibleSubtractTwoAreas()
        {
            var area = Area.FromSquareMeters(10);
            var area2 = Area.FromSquareMeters(5);

            var expected = Area.FromSquareMeters(5);

            Assert.AreEqual(expected, area - area2);
        }

        [TestMethod]
        public void MustBePossibleSubtractSquareMetersFromAnArea()
        {
            var area = Area.FromSquareMeters(10);

            var expected = Area.FromSquareMeters(5);

            Assert.AreEqual(expected, area - 5);
        }

        [TestMethod]
        public void MustBePossibleMultiplyTwoAreas()
        {
            var area = Area.FromSquareMeters(10);
            var area2 = Area.FromSquareMeters(10);

            var expected = Area.FromSquareMeters(100);

            Assert.AreEqual(expected, area * area2);
        }

        [TestMethod]
        public void MustBePossibleMultiplyAnAreaToADecimalNumber()
        {
            var area = Area.FromSquareMeters(10);

            var expected = Area.FromSquareMeters(100);

            Assert.AreEqual(expected, area * 10);
        }

        [TestMethod]
        public void MustBePossibleDivideTwoAreas()
        {
            var area = Area.FromSquareMeters(100);
            var area2 = Area.FromSquareMeters(50);

            var expected = Area.FromSquareMeters(2);

            Assert.AreEqual(expected, area / area2);
        }

        [TestMethod]
        public void MustBePossibleDivideAnAreaToADecimalNumber()
        {
            var area = Area.FromSquareMeters(100);

            var expected = Area.FromSquareMeters(2);

            Assert.AreEqual(expected, area / 50);
        }

        [TestMethod]
        public void MustBePossibleCompareIfAnAreaIsGreaterOrEqualOtherArea()
        {
            var area = Area.FromSquareMeters(100);
            var area2 = Area.FromSquareMeters(50);
            var area3 = Area.FromSquareMeters(100);

            Assert.IsTrue(area >= area2);
            Assert.IsTrue(area >= area3);
            Assert.IsFalse(area2 >= area3);
        }

        [TestMethod]
        public void MustBePossibleCompareIfAnAreaIsLessThanOrEqualOtherArea()
        {
            var area = Area.FromSquareMeters(50);
            var area2 = Area.FromSquareMeters(50);
            var area3 = Area.FromSquareMeters(100);

            Assert.IsTrue(area <= area2);
            Assert.IsTrue(area <= area3);
            Assert.IsFalse(area3 <= area2);
        }

        [TestMethod]
        public void MustBePossibleCompareIfAnAreaIsGreaterThanOtherArea()
        {
            var area = Area.FromSquareMeters(100);
            var area2 = Area.FromSquareMeters(50);

            Assert.IsTrue(area > area2);
            Assert.IsFalse(area2 > area);
        }

        [TestMethod]
        public void MustBePossibleCompareIfAnAreaIsLessThanOtherArea()
        {
            var area = Area.FromSquareMeters(50);
            var area2 = Area.FromSquareMeters(100);

            Assert.IsTrue(area < area2);
            Assert.IsFalse(area2 < area);
        }
    }
}
```

E segundo, a implementação de Area:

```csharp
using System;

namespace DDDLibrary
{
    public class Area : IEquatable<Area>
    {
        private const decimal HectareInSquareMeters = 10000;

        private const decimal AcreInSquareMeters = 4046.856422m;

        private const decimal AlqueireDoNorteInSquareMeters = 27000.225m;

        private const decimal AlqueirePaulistaInSquareMeters = 24000.200m;

        private const decimal AlqueireMineiroInSquareMeters = 48000.400m;

        private readonly decimal _squareMeters;

        private Area(decimal squareMeteres)
        {
            _squareMeters = squareMeteres;
        }

        public static Area FromSquareMeters(decimal squareMeters)
        {
            return new Area(squareMeters);
        }

        public static Area FromHectares(decimal hectares)
        {
            return new Area(hectares * HectareInSquareMeters);
        }

        public static Area FromAcres(decimal acres)
        {
            return new Area(acres * AcreInSquareMeters);
        }

        public static Area FromAlqueiresDoNorte(decimal alqueiresDoNorte)
        {
            return new Area(alqueiresDoNorte * AlqueireDoNorteInSquareMeters);
        }

        public static Area FromAlqueiresPaulista(decimal alqueiresPaulista)
        {
            return new Area(alqueiresPaulista * AlqueirePaulistaInSquareMeters);
        }

        public static Area FromAlqueiresMineiro(decimal alqueiresMineiro)
        {
            return new Area(alqueiresMineiro * AlqueireMineiroInSquareMeters);
        }

        public bool Equals(Area other)
        {
            if (other == null)
                return false;

            if (ReferenceEquals(this, other))
                return true;

            return InSquareMeters().Equals(other.InSquareMeters());
        }

        public override bool Equals(object obj)
        {
            if (obj == null)
                return false;

            if (ReferenceEquals(this, obj))
                return false;

            var area = obj as Area;

            return area != null && Equals(area);
        }

        public override int GetHashCode()
        {
            return _squareMeters.GetHashCode();
        }

        public static Area operator +(Area area1, Area area2)
        {
            return FromSquareMeters(area1.InSquareMeters() + area2.InSquareMeters());
        }

        public static Area operator +(Area area, decimal squareMeters)
        {
            return FromSquareMeters(area.InSquareMeters() + squareMeters);
        }

        public static Area operator -(Area area, Area area2)
        {
            return FromSquareMeters(area.InSquareMeters() - area2.InSquareMeters());
        }

        public static Area operator -(Area area, decimal squareMeters)
        {
            return FromSquareMeters(area.InSquareMeters() - squareMeters);
        }

        public static Area operator *(Area area, Area area2)
        {
            return FromSquareMeters(area.InSquareMeters() * area2.InSquareMeters());
        }

        public static Area operator *(Area area, decimal multiplier)
        {
            return FromSquareMeters(area.InSquareMeters() * multiplier);
        }

        public static Area operator /(Area area, Area area2)
        {
            return FromSquareMeters(area.InSquareMeters() / area2.InSquareMeters());
        }

        public static Area operator /(Area area, decimal divider)
        {
            return FromSquareMeters(area.InSquareMeters() / divider);
        }

        public static bool operator ==(Area left, Area right)
        {
            return Equals(left, null) ? (Equals(right, null)) : left.Equals(right);
        }

        public static bool operator !=(Area left, Area right)
        {
            return !(left == right);
        }

        public static bool operator >=(Area area, Area area2)
        {
            return area.InSquareMeters() >= area2.InSquareMeters();
        }

        public static bool operator <=(Area area, Area area2)
        {
            return area.InSquareMeters() <= area2.InSquareMeters();
        }

        public static bool operator >(Area area, Area area2)
        {
            return area.InSquareMeters() > area2.InSquareMeters();
        }

        public static bool operator <(Area area, Area area2)
        {
            return area.InSquareMeters() < area2.InSquareMeters();
        }

        public decimal InSquareMeters()
        {
            return _squareMeters;
        }

        public decimal InHectares()
        {
            return _squareMeters / HectareInSquareMeters;
        }

        public decimal InAcres()
        {
            return _squareMeters / AcreInSquareMeters;
        }

        public decimal InAlqueiresDoNorte()
        {
            return _squareMeters / AlqueireDoNorteInSquareMeters;
        }

        public decimal InAlqueiresPaulista()
        {
            return _squareMeters / AlqueirePaulistaInSquareMeters;
        }

        public decimal InAlqueiresMineiro()
        {
            return _squareMeters / AlqueireMineiroInSquareMeters;
        }
    }
}
```

Bem, não vou explicar com detalhes a implementação pois os testes já fazem isso, como resultado final poderemos por exemplo comparar as áreas de dois terrenos sem importar-se com a conversão de Metros pra Hectares, ou Alqueires, etc. O código completo demonstrado nesse post pode ser encontrado aqui no meu github:

[https://github.com/emersonsoares/ValueObjectCSharp](https://github.com/emersonsoares/ValueObjectCSharp)
