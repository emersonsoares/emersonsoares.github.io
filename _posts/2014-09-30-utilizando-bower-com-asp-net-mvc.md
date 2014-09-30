---
layout:     post
title:      Utilizando Bower com ASP .NET MVC
date:       2014-09-30 11:00:00
summary:    Utilize o Bower para gerenciar suas dependências de frontend e seja mais feliz
categories: asp .net mvc, bower
permalink: /utilizando-bower-com-asp-net-mvc/
---

Eu sempre notei que a maioria dos desenvolvedores .NET nunca ou quase nunca buscam ferramentas, frameworks ou qualquer outra coisa que não seja o que a Microsoft oferece, que não venha com o Visual Studio... O Victor Cavalcante falou disso em um [grupo sobre ASP .NET MVC](https://www.facebook.com/groups/aspnetmvcbr/permalink/713959755348640/) no Facebook também.

Hoje temos ferramentas maravilhosas fora do mundo .NET que podem ser utilizadas em projetos .NET por exemplo: Bower, Gulp, Yeoman etc. A Microsoft está começando a nos levar para esse lado também, hoje já temos o Visual Studio trabalhando lindamente com o GIT, em breve teremos o ASP .NET rodando em qualquer plataforma [(ASP .NET VNEXT)](http://www.asp.net/vnext) e provavelmente teremos no Visual Studio “14” uma maior integração com essas ferramentas como podemos ver nesse post do Hanselman: [Introducing Gulp, Grunt, Bower, and npm support for Visual Studio](http://www.hanselman.com/blog/introducinggulpgruntbowerandnpmsupportforvisualstudio.aspx). Vou falar sobre como podemos utilizar especificamente o Bower para gerenciar bibliotecas client side.

Quando decidi utilizar o Bower para gerenciar bibliotecas client side nos meus projetos, tive que sair pesquisando formas de utilizar isso em conjunto com o Visual Studio, encontrei varias opções e vou explicar aqui a forma como estou fazendo.

Primeiramente o [Bower](http://bower.io/) é um gerenciador de bibliotecas de frontend, como o [NuGet](http://www.nuget.org/) é para os packages .NET. 

Atualmente a maioria dos projetos ASP .NET MVC utiliza o NuGet para gerenciar essas bibliotecas, o próprio template de projeto web do Visual Studio já utiliza o NuGet para gerenciar os packages do Jquery, JqueryValidation, Respond.Js, Bootstrap etc. 
Acontece que já temos todo um ecossistema altamente capaz de fazer isso de forma otimizada e madura e por isso queremos manter o NuGet apenas para gerenciar packages .NET.

O Bower roda em cima do Node.Js e é instalado através do NPM que é gerenciador de packages Node. 

O primeiro passo então é instalar o Node.Js, ele funciona normalmente no Windows e pode ser instalado através do download do [instalador no site oficial](http://nodejs.org/).

Após a instalação do Node o próximo passo é a instalação do Bower. Como eu disse o Bower é um package Node e por isso ele será instalado via [NPM](https://www.npmjs.org). Rode o comando:

```
npm install -g bower
```

Pronto, agora temos o Bower instalado globalmente no nosso ambiente Node, e ele pode ser usado de qualquer lugar na máquina.

O próximo passo é configurar o Bower no nosso projeto ASP .NET MVC. O ```.bowerrc``` é um arquivo JSON onde ficam armazenadas configurações para o Bower. Por hora vamos adicionar apenas uma configuração, que é o diretório onde queremos que o Bower armazene as bibliotecas baixadas. Adicione no ```.bowerrc``` a seguinte configuração:

```
{
  "directory" : "Content/components"
}
```

Assim estamos dizendo ao Bower para colocar as bibliotecas no diretorio ```Content/components```, você pode configurar isso da forma que preferir.

Falamos do ```.bowerrc``` que é o arquivo que define configurações para o Bower, agora o próximo arquivo utilizado pelo será o ```bower.json``` que é onde definimos configurações para o **projeto**.

Você pode criar esse arquivo manualmente e adiciona-lo na raiz do projeto, ou pode rodar o comando

```
bower init
```

que cria esse arquivo já com algumas configurações definidas. No arquivo ```bower.json``` existe a seção ```dependencies``` que é onde definimos as bibliotecas que são dependencias do projeto. Vamos adicionar por exemplo o Jquery e o Bootstrap como dependencias em um projeto:

```
{
    "name" :  "WebApplication",
    "dependencies": {
        "jquery": "~2.1.1",
        "bootstrap": "~3.2.0"
    }
}
```

Com o ```bower.json``` configurado é só rodar o comando:

```
bower install
```

Esse comando vai ler todas as dependencias no arquivo ```bower.json```, fazer o download e instalar todas no diretório ```Content/components```. Outra opção mais simples é instalar as bibliotecas e fazer com que o bower automaticamente adicione a biblioteca instalada como dependencia no arquivo ```bower.json```. Isso pode ser feito através do comando:

```
bower install <package> -S
```

Onde ```<package>``` é o nome da biblioteca a ser instalada.

No arquivo ```bower.json``` é possivel adicionar muitas outras informações sobre o projeto, veja ele como um manifest file. Você pode ver mais detalhes na [documentação oficial](http://bower.io/docs/creating-packages/).

Sempre que você instalar um novo package no projeto deverá adicionar ele manualmente no Visual Studio.
Na solution explorer, clique em ```Show All Files``` e vá na pasta ```Content```

<img src="/assets/utilizando-bower-com-asp-net-mvc-1.png" alt="Visual Studio Solution Explorer" style="width:338px;height:366px;">

Veremos dentro a pasta ```components``` e dentro dela as pastas de cada biblioteca adicionada. Normalmente os packages Bower contém arquivos de configuração, arquivos de código fonte da biblioteca e uma pasta ```dist``` que é onde fica a versão de produção da biblioteca. Eu prefiro adicionar à solution do Visual Studio apenas essa pasta de cada biblioteca mas isso vai depender da sua necessidade. 

Bom por enquanto é assim que funciona para mim. O Giovanni Bassi criou um package no NuGet que instala o Bower e NPM dentro do seu projeto e ele fica disponivel diretamente no Package Manager. É uma opção bacana também, vou deixar o link aqui: [Bower e NPM na linha de comando dentro do Visual Studio instalado via Nuget](http://blog.lambda3.com.br/2013/12/bower-e-npm-na-linha-de-comando-dentro-do-visual-studio-instalado-via-nuget/).

Não importa a forma como você vai utilizar o importante é que comece a utilizar no seu workflow as ferramentas mais apropriadas e não fique somente no que vem na "caixinha" com o Visual Studio.
