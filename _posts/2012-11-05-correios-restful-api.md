---
layout:     post
title:      Correios RESTful API
date:       2012-11-05 10:59:42
summary:    API para pesquisa de endereços por cep diretamente na base dos Correios
categories: asp .net mvc
permalink: /correios-restful-api/
---

Olá pessoal como podem ver ando meio sem tempo pro blog mas esse tempo está sendo bem gasto, com varias horas de desenvolvimento hehe, e nesse post venho mostrar um resultado disso. 
No projeto em que estou trabalhando atualmente surgiu a necessidade daquela funcionalidade de autocompletar um endereço a partir de um cep informado pelo usuário, como os correios não tem uma API para isso e o banco de dados deles é bem caro resolvi fazer uma API open source para usarmos em nosso projeto e para que outros desenvolvedores também possam usá-la.

A API está hospedada em: [http://correiosapi.apphb.com/](http://correiosapi.apphb.com/)

Para fazer uma pesquisa de CEP tente algo como:

```javascript
$.ajax({
  url: 'http://correiosapi.apphb.com/cep/76873274',
  dataType: 'jsonp',
  crossDomain: true,
  contentType: "application/json",
  statusCode: {
    200: function(data) { console.log(data); } // Ok
    ,400: function(msg) { console.log(msg);  } // Bad Request
    ,404: function(msg) { console.log("CEP não encontrado!!"); } // Not Found
  }
});​
```

Se o cep buscado for encontrado você terá como resultado o um objeto json:

```javascript
{
    bairro: "Setor Institucional"
    cep: "76872862"
    cidade: "Ariquemes"
    estado: "RO"
    logradouro: "Rio Madeira "
    tipodelogradouro: "Rua"
}
```

Bem, aí você me pergunta, mas de onde vem esses dados já que os correios não tem uma API publica?

Resposta: Os dados vem direto dos correios mesmo! A API faz um web request no link [http://m.correios.com.br/movel/buscaCepConfirma.do](http://m.correios.com.br/movel/buscaCepConfirma.do), e um "crawler" trata o html retornado para extrair as informações do endereço.

Basicamente é isso, o código fonte desse projeto pode ser encontrado em meu github: [https://github.com/emersonsoares](https://github.com/emersonsoares) caso você queria hospedar a API em um servidor próprio sinta-se a vontade, pull-requests serão bem vindos também.

Ah! Mais um detalhe, a API foi desenvolvida utilizando Asp.Net WebAPI.