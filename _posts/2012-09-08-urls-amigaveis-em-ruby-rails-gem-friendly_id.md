---
layout:     post
title:      Url's amigáveis em Ruby on Rails com a gem Friendly_id 
date:       2012-09-08 10:08:04
summary:    Implementação de urls amigáveis em Ruby on Rails
categories: ruby
permalink: /urls-amigaveis-em-ruby-rails-gem-friendly_id/
---

Nos últimos dias venho me aventurando pelo Framework Rails e tenho gostado muito principalmente pela maturidade do Framework e pela praticidade da linguagem Ruby, bem mas isso é assunto para outro post... 
No decorrer do desenvolvimento de um site, senti a necessidade de gerar uma url's amigáveis, por padrão o Rails gera url's nesse modelo: *[http://meusite/posts/1](http://meusite/posts/1)*, ou seja, identificando um registro pelo seu Id na url, vamos concordar que isso não é nem um pouco interessante nem para o úsuario final e nem para os motores de busca, eu precisava de um resultado parecido com esse: *[http://meusite/um-exemplo-de-post](http://meusite/um-exemplo-de-post)*, foi ai então que resolvi buscar um solução para isso. 

Logo nos primeiros resultados percebi que muitos desenvolvedores tomam o caminho mais fácil para lidar com url's amigáveis, eles usam o Id do registro seguido por um hífen e alguma sequencia de caracteres após o hífen geralmente algo como o titulo no caso de uma noticia ou post de blog, obtendo um resultado parecido com esse: *[http://meusite/posts/1322-um-exemplo-de-post](http://meusite/posts/1322-um-exemplo-de-post)*.

Essa forma geralmente é utilizada com o método do Ruby [to_i](http://www.ruby-doc.org/core-1.9.3/String.html#method-i-to_i) que basicamente converte uma string para um inteiro removendo quaisquer caracteres não numéricos. No *controller* teríamos algo assim:

```ruby
#http://meusite/posts/1322-um-exemplo-de-post
 
Post.find(params[:id].to_i)
```

É uma solução pratica mas na minha opinião a url ainda não está tão amigável assim, veja que ainda existe o Id do post no inicio da url o que a torna difícil de memorizar, e deixa a url com um numero inútil para os motores de busca. Para resolver isso encontrei a gem [friendly_id](https://github.com/norman/friendly_id) que faz esse trabalho de forma simples:

Em seu gemfile coloque o seguinte código:

```ruby
gem "friendly_id", "~> 3.1"
```

Então execute os seguintes comandos:

```shell
bundle install
rails g friendly_id
rake db:migrate
```

Feito isso teremos a gem [friendly_id](https://github.com/norman/friendly_id) instalada em nosso projeto, o próximo passo é adicionar o seguinte código ao model no qual você quer utilizar url's amigáveis:

```ruby
has_friendly_id :title, :use_slug => true
```

E pronto! Agora é só rodar seu projeto e ver mágica. O [friendly_id](https://github.com/norman/friendly_id) tem mais algumas configurações que podem ser encontradas na sua [documentação no github](https://github.com/norman/friendly_id).