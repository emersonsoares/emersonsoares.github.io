---
layout:     post
title:      CakePHP – ThumbnailHelper, um gerador de miniaturas com cache dinâmico
date:       2012-05-10 00:50:25
summary:    Helper para renderização e cache de imagens
categories: cakephp
permalink: /cakephp-thumbnailhelper-um-gerador-de-miniaturas-com-cache-dinamico/
---

Em todo site que já trabalhei tive o problema das miniaturas de imagens, pois sempre temos que ter miniaturas de certas imagens para exibi-las nas views, no cakephp não foi diferente encontrei o behavior meio upload que faz toda a parte de uploads de imagens, mas para gerar miniaturas ele usa o [phpthumb](http://sourceforge.net/projects/phpthumb/)  mas não consegui usa-lo de jeito nenhum sem contar que ele iria gerar a miniatura da imagem na hora do upload, mas muitas vezes precisamos dessas miniaturas em vários tamanhos diferentes e não fica viável criar varias miniaturas, assim criei um helper que me "serve" essas miniaturas de acordo com os parâmetros passados, como tamanho, altura, qualidade e método de corte, sempre que ele me serve uma miniatura baseada nesses parâmetros ele cria um cache dessa imagem assim gerando ela somente uma vez, economizando recursos do servidor e aumentando a performance. O uso do ThumbnailHelper ficará assim:

```php
echo $this->Thumbnail->render('teste.jpg', array(
        'path' => '',
        'width' => '100',
        'height' => '100',
        'resizeOption' => 'portrait',
        'quality' => '100'
            ), array('id' => 'img-test', 'alt' => 'thumbnail test'));
```

Para fazer o download do helper visite o meu github, faça um clone do repositório dentro da pasta app/View/Helper.

[Thumbanils Plugin](https://github.com/emersonsoares/ThumbnailsPlugin)