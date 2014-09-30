---
layout:     post
title:      Utilizando Bower com ASP .NET MVC
date:       2014-09-30 11:00:00
summary:    Utilize o Bower para gerenciar suas dependências de frontend e seja mais feliz
categories: asp .net mvc, bower
permalink: /utilizando-bower-com-asp-net-mvc/
---

Eu sempre notei que a maioria dos desenvolvedores .NET nunca ou quase nunca buscam ferramentas, frameworks ou qualquer outra coisa que não seja o que a Microsoft oferece, que não venha com o Visual Studio... O Victor Cavalcante falou disso em um [grupo sobre ASP .NET MVC](https://www.facebook.com/groups/aspnetmvcbr/permalink/713959755348640/) no Facebook também.

Hoje temos ferramentas maravilhosas fora do mundo .NET que podem ser utilizadas em projetos .NET por exemplo: Bower, Gulp, Yeoman etc. A Microsoft está começando a nos levar para esse lado também, hoje já temos o Visual Studio trabalhando lindamente com o GIT, em breve teremos o ASP .NET rodando em qualquer plataforma [(ASP .NET VNEXT)](http://www.asp.net/vnext) e provavelmente teremos no Visual Studio “14” uma maior integração com essas ferramentas como podemos ver nesse post do Hanselman: [Introducing Gulp, Grunt, Bower, and npm support for Visual Studio](http://www.hanselman.com/blog/introducinggulpgruntbowerandnpmsupportforvisualstudio.aspx).

Vou falar sobre como podemos utilizar especificamente o Bower para gerenciar bibliotecas client side.

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

Assim estamos dizendo ao Bower para colocar as bibliotecas no diretorio Content/components.
