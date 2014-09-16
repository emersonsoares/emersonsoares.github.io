---
layout:     post
title:      CakePHP - VideoHelper um helper para gerar embed de videos a partir da url
date:       2012-05-10 01:11:51
summary:    Helper para exibição de videos embedados na página
categories: cakephp
permalink: /cakephp-videohelper-um-helper-para-gerar-embed-de-videos-a-partir-da-url/
---

Aqui estou novamente pra falar de [cakephp](http://cakephp.org/), como atualmente estou desenvolvendo um projeto em cake sempre estão surgindo novas necessidades em relação a componentes e etc, e dessa vez eu precisava criar um módulo para videos no cms. 
Certo! Como ele funciona: o usuario ira cadastrar titulo, url e uma breve descrição do video, até ai tudo bem, mas e para exibir o player desse video no meu site ?

A resposta é simples, preciso de um embed desse vídeo na minha pagina, e é exatamente isso que eu irei mostrar como fazer utilizando o "VideoHelper"!

Primeiro faça um clone do repositorio onde está hospedado o helper nesse link: [https://github.com/emersonsoares/VideosHelper-for-cakephp](https://github.com/emersonsoares/VideosHelper-for-cakephp) para dentro da pasta *app/View/Helper*, caso você não use o [git](http://git-scm.com/) não tem problema é só baixar como zip o helper [aqui](https://github.com/emersonsoares/VideosHelper-for-cakephp/zipball/master).

Segundo, declare o uso do helper no seu AppControler:

```php
//app/Controler/AppControler.php
public $helpers = array('Html', 'Video');
```

Terceiro, vá na sua view e use o helper! Assim óh:

```php
// app/View/MyController/myaction.ctp
echo $this->Video->embed('http://minhaurldovideo', array(
        'width' => 450,
        'height' => 300
));
```

Ou se quiser uma coisa mais avançada:

```php
// Alguns desses parâmetros não servem para certos videos, ou seja, eles serão ignorados.
echo $this->Video->embed('http://minhaurldovideo', array(
        'width' => 450,
        'height' => 300,
        'allowfullscreen'=>1,
        'loop'=>1,
        'color'=>'00adef',
        'show_title'=>1,
        'show_byline'=>1,
        'show_portrait'=>0,
        'autoplay'=>1,
        'frameborder'=>0
)) ;
```

Quando for cadastrar esse vídeo lá no [CMS](http://pt.wikipedia.org/wiki/Sistema_de_gerenciamento_de_conte%C3%BAdo), o usuário não precisará escolher qual é o site do video e muito menos precisar ir la e gerar o embed pelo próprio site, ele só precisa copiar a url do vídeo e cadastrar, o resto o helper que faz.

Lembrando que é compatível com videos do YouTube e do Vimeo por enquanto, já está programado uma atualização para aceitar videos do RedTube também :P, se alguém tiver mais alguma sugestão de serviços de videos é só deixar nos comentários ou você mesmo pode fazer e mandar um pull request pra gente la no [github](http://github.com/emersonsoares).