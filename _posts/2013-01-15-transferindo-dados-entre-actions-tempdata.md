---
layout:     post
title:      Transferindo dados entre Actions utilizando TempData
date:       2013-01-15 14:23:45
summary:    Esse post é um snippet para mostrar como utilizar o recurso TempData do Asp.Net MVC para transportar dados entre diferentes Actions.
categories: asp .net mvc
permalink: /transferindo-dados-entre-actions-tempdata/
---

Esse post é um snippet para mostrar como utilizar o recurso TempData do Asp.Net MVC para transportar dados entre diferentes Actions.

Imagine que em uma action após fazer um cadastro de usuário você precisa fazer um redirecionamento para a Action  de login e ainda enviar uma mensagem de sucesso.

Veja que aqui estamos na Action **Cadastro** onde executamos a rotina de cadastro do usuário e logo após iremos redirecionar o usuário para a Action **Login** enviando uma mensagem utilizando TempData.

```csharp
public ActionResult Cadastro(Usuario usuario)
{
  //...Rotina de cadastro
 
  TempData["Mensagem"] = "Registro concluido, o seu cadastro irá para aprovação e você será informado por email.";
  return RedirectToAction("Login", "Autenticacao");
}
```

Aqui na **View Login** exibimos a mensagem que veio da Action **Cadastro:**

```csharp
<div class="alert alert-info">@TempData["Mensagem"]</div>
```