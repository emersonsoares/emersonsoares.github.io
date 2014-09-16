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

<div>[gist id=6527172 file=AreaTests.cs]</div>

E segundo, a implementação de Area:

[gist id=6527172 file=Area.cs]

Bem, não vou explicar com detalhes a implementação pois os testes já fazem isso, como resultado final poderemos por exemplo comparar as áreas de dois terrenos sem importar-se com a conversão de Metros pra Hectares, ou Alqueires, etc. O código completo demonstrado nesse post pode ser encontrado aqui no meu github:

[https://github.com/emersonsoares/ValueObjectCSharp](https://github.com/emersonsoares/ValueObjectCSharp)
