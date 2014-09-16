---
layout:     post
title:      .gitignore não funciona? Como resolver.
date:       2012-05-10 22:57:09
summary:    Resolvendo problemas com o .gitignore
categories: git
permalink: /gitignore-nao-funciona-como-resolver/
---

No inicio de um projeto com Visual Studio eu criei um novo repositório git e fiz vários commits, o que aconteceu foi que lá na frente eu percebi que devia ter ignorado alguns arquivos que não são necessários ser versionados, adicionei o .gitignore ao projeto e pra minha surpresa ele não quis funcionar, pois eu já havia comitado esses arquivos, tive uma baita dor de cabeça para descobrir o motivo, e como resolver, mas descobri, a solução é a seguinte:

```
$ git rm -r --cached .
$ git add .
```

Ele remove todos os arquivos do índice sem alterá-los no seu diretório de trabalho e, em seguida, adiciona esses arquivos novamente ao índice, ignorando todos aqueles listados no .gitignore.

[Referencia](http://stackoverflow.com/questions/1139762/gitignore-file-not-ignoring)