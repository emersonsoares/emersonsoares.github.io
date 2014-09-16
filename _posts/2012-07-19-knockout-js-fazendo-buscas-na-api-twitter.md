---
layout:     post
title:      Fazendo buscas na api do twitter com knockout.js
date:       2012-07-19 15:01:11
summary:    Neste post mostro um exemplo simples e um pouco do poder do knockout.js. Utilizando a api do twitter, vamos fazer uma busca por um termo e exibir os resultados em uma view através do knockout.
categories: javascript
permalink: /knockout-js-fazendo-buscas-na-api-twitter/
---

Bem primeiro o que é o knockout.js ?

Segundo o site oficial:

>Knockout is a JavaScript library that helps you to create user interfaces, rich and responsive Viewer and editor with a clean underlying data model. Any time that you have sections of the user interface that update dynamically (for example, changing depending on the actions of the user or when you change the external data source), KO can help you implement it more simple and maintainably.

Neste post mostro um exemplo simples e um pouco do poder do knockout.js. Utilizando a api do twitter, vamos fazer uma busca por um termo e exibir os resultados em uma view através do knockout.

No arquivo app.js temos o nosso ViewModel,

```javascript
jQuery(function($) {
 
    var TwitterViewModel = function() {
        var self = this;
 
        self.query = ko.observable();
        self.tweets = ko.observableArray();
        self.currentPage = ko.observable(1);
 
        self.searchTweets = function() {
            self.tweets.removeAll();
            self.getTweets(self.query);
        };
 
        self.getTweets = function(query, currentPage) {
            $.ajax({
                dataType: 'jsonp'
                ,url: 'http://search.twitter.com/search.json'
                ,data: { q: query, include_entities: true, page: currentPage, rpp: 10}
                ,success: function(json) {
                    $.each(json.results, function(i, current){
                        self.tweets.push({ 
                            tweet: current.text,
                            user: current.from_user,
							created_at: current.created_at
                        });
                    });
                    self.currentPage(json.page);
                }
                ,error: function( jqXHR, textStatus, errorThrown ) {
                    console.log( 'error: ' + textStatus);
                }
                ,timeout:5000
                ,retryMax: 2
            });
        };
    };
 
    var viewModel = new TwitterViewModel();
    ko.applyBindings(viewModel);
 
    $(window).scroll(function () {
        if ($(this).height() + $(this).scrollTop() == $(document).height()) {
            viewModel.getTweets(viewModel.query, viewModel.currentPage() + 1);
        }
    });
 
});
```

E na nossa index.html temos a nossa view com os bindings:

```html
<div class="span12 well">
<!-- ko foreach: tweets -->
    <div class="alert alert-info alert-block">
        <blockquote>
            <span data-bind="text: tweet"></span>
        </blockquote>
        <h5 class="alert-heading span2">
            @<i data-bind="text: user"></i>
        </h5>
	<h6 data-bind="text: created_at"></h6>
    </div>
<!-- /ko -->
</div>
```

Para ver este exemplo funcionando este  é o link: [http://knockout-twitter-search-sample.herokuapp.com](http://knockout-twitter-search-sample.herokuapp.com)

O código fonte completo está no [meu github](https://github.com/emersonsoares)