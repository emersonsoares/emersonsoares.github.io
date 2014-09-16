---
layout:     post
title:      Git - Removendo branchs locais e remotos
date:       2012-07-08 23:05:52
summary:    Como remover branchs locais e remotos no git
categories: git
permalink: /git-removendo-branchs-locais-remotos/
---

O post é simples de propósito, o objetivo é ir registrando aqui dicas rápidas sobre coisas, que a gente acaba esquecendo no dia-a-dia, ou não conhece ainda.

Remover uma branch local:

```
git branch -d minhabranch
```

Remover uma branch no repositorio remoto:

```
git push origin :minhabranch
```