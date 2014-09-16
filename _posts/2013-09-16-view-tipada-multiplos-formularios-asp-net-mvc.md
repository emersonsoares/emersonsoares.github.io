---
layout:     post
title:      View tipada e múltiplos formulários em Asp .Net MVC
date:       2013-09-16 18:48:18
summary:    Implementação de uma view tipada com muitos formulários
categories: asp .net mvc
permalink: /view-tipada-multiplos-formularios-asp-net-mvc/
---

Um caso muito comum onde precisamos utilizar dois formulários em uma mesma View tipada, pode ser em uma tela de Login ou Registro de usuários por exemplo. Nesses casos muita gente acaba sem saber o que fazer para utilizar views tipadas e todos os benefícios que temos com elas.

Já precisei resolver esta situação há algum tempo atrás e frequentemente vejo essa pergunta em alguns grupos que participo, bem eu resolvi da seguinte forma.

Implementação da View Index que contém os dois formulários:

```csharp
@model MultipleFormsInTheSameViewSample.ViewModels.RegisterOrLoginViewModel
 
@{
    ViewBag.Title = "Index";
    Layout = "~/Views/Shared/_Layout.cshtml";
}
 
<h1>Exemplo de View Tipada com mais de um formulário</h1>
 
@using (Html.BeginForm("Login", "Membership"))
{
    <legend>Login</legend>
    @Html.LabelFor(m => m.LoginEmail)
    @Html.TextBoxFor(m => m.LoginEmail)
    @Html.ValidationMessageFor(m => m.LoginEmail)
 
    @Html.LabelFor(m => m.LoginPassword)
    @Html.PasswordFor(m => m.LoginPassword)
    @Html.ValidationMessageFor(m => m.LoginPassword)
 
    <input type='Submit' value='Login' />
}
 
@using (Html.BeginForm("Register", "Membership"))
{
    <legend>Registro</legend>
    @Html.LabelFor(m => m.RegisterFirstName)
    @Html.TextBoxFor(m => m.RegisterFirstName)
    @Html.ValidationMessageFor(m => m.RegisterFirstName)
 
    @Html.LabelFor(m => m.RegisterLastName)
    @Html.TextBoxFor(m => m.RegisterLastName)
    @Html.ValidationMessageFor(m => m.RegisterLastName)
    
    @Html.LabelFor(m => m.RegisterEmail)
    @Html.TextBoxFor(m => m.RegisterEmail)
    @Html.ValidationMessageFor(m => m.RegisterEmail)
 
    @Html.LabelFor(m => m.RegisterPassword)
    @Html.PasswordFor(m => m.RegisterPassword)
    @Html.ValidationMessageFor(m => m.RegisterPassword)
    <input type='Submit' value='Register' />
}
@section scripts
{
    @Scripts.Render("~/bundles/jqueryval")
}
```

Controller responsável por servir a View Index e receber o post para Login e Registro:

```csharp
using System.Web.Mvc;
using MultipleFormsInTheSameViewSample.Filters;
using MultipleFormsInTheSameViewSample.ViewModels;
 
namespace MultipleFormsInTheSameViewSample.Controllers
{
    public class MembershipController : Controller
    {
        [ImportModelStateFromTempData]
        public ActionResult Index()
        {
            return View();
        }
 
        [HttpPost]
        [IgnoreModelErrors("Login")]
        [ExportModelStateToTempData]
        public ActionResult Register(RegisterOrLoginViewModel user)
        {
            //Vamos forçar um erro, para testar a validação
            ModelState.AddModelError("RegisterEmail", "EmailInvalido");
 
            if (ModelState.IsValid)
                return RedirectToAction("Success");
 
            return RedirectToAction("Index");
        }
 
        [HttpPost]
        [IgnoreModelErrors("Register")]
        [ExportModelStateToTempData]
        public ActionResult Login(RegisterOrLoginViewModel user)
        {
            //Vamos forçar um erro, para testar a validação
            ModelState.AddModelError("LoginEmail", "EmailInvalido");
 
            if (ModelState.IsValid)
                return RedirectToAction("Success");
 
            return RedirectToAction("Index");
        }
 
        public ActionResult Success()
        {
            return View();
        }
    }
}
```

ViewModel que representa os dois formulários na View Index:

```csharp
using System.ComponentModel.DataAnnotations;
 
namespace MultipleFormsInTheSameViewSample.ViewModels
{
    public class RegisterOrLoginViewModel
    {
        #region Properties for Login
 
        [Required(ErrorMessage = "Digite um email")]
        [RegularExpression(@"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", ErrorMessage = "Email inválido")]
        public string LoginEmail { get; set; }
 
        [Required(ErrorMessage = "Digite uma senha")]
        [MinLength(5, ErrorMessage = "A senha deve ter no máximo 20 caracteres")]
        [MaxLength(20, ErrorMessage = "A senha deve ter no máximo 20 caracteres")]
        public string LoginPassword { get; set; }
 
        #endregion
 
        #region Properties for Register
 
        [Required(ErrorMessage = "Digite um email")]
        [RegularExpression(@"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", ErrorMessage = "Email inválido")]
        public string RegisterEmail { get; set; }
 
        [Required(ErrorMessage = "Digite uma senha")]
        [MinLength(5, ErrorMessage = "A senha deve ter no máximo 20 caracteres")]
        [MaxLength(20, ErrorMessage = "A senha deve ter no máximo 20 caracteres")]
        public string RegisterPassword { get; set; }
 
        [Required(ErrorMessage = "Digite o primeiro nome")]
        public string RegisterFirstName { get; set; }
 
        [Required(ErrorMessage = "Digite o segundo nome")]
        public string RegisterLastName { get; set; }
 
        #endregion
    }
}
```

Action Filter responsável por exportar o Model State para a View de destino em um Redirect:

```csharp
/* 
 * Thanks to: http://weblogs.asp.net/rashid/archive/2009/04/01/asp-net-mvc-best-practices-part-1.aspx#prg
 */
 
using System.Web.Mvc;
 
namespace MultipleFormsInTheSameViewSample.Filters
{
    public abstract class ModelStateTempDataTransfer : ActionFilterAttribute
    {
        protected static readonly string Key = typeof(ModelStateTempDataTransfer).FullName;
    }
 
    public class ExportModelStateToTempData : ModelStateTempDataTransfer
    {
        public override void OnActionExecuted(ActionExecutedContext filterContext)
        {
            if (!filterContext.Controller.ViewData.ModelState.IsValid)
                if ((filterContext.Result is RedirectResult) || (filterContext.Result is RedirectToRouteResult))
                    filterContext.Controller.TempData[Key] = filterContext.Controller.ViewData.ModelState;
 
            if (!string.IsNullOrEmpty(filterContext.Controller.ViewBag.Message))
                filterContext.Controller.TempData["Message"] = filterContext.Controller.ViewBag.Message;
 
            base.OnActionExecuted(filterContext);
        }
    }
 
    public class ImportModelStateFromTempData : ModelStateTempDataTransfer
    {
        public override void OnActionExecuted(ActionExecutedContext filterContext)
        {
            var modelState = filterContext.Controller.TempData[Key] as ModelStateDictionary;
 
            if (modelState != null)
                if (filterContext.Result is ViewResult)
                    filterContext.Controller.ViewData.ModelState.Merge(modelState);
                else
                    filterContext.Controller.TempData.Remove(Key);
 
            if (!string.IsNullOrEmpty((string)filterContext.Controller.TempData["Message"]))
                filterContext.Controller.ViewBag.Message = filterContext.Controller.TempData["Message"];
 
            base.OnActionExecuted(filterContext);
        }
    }
}
```

Action Filter responsável por permitir a validação parcial de um model.

```csharp
/* 
 * Thanks to: http://mrbigglesworth79.blogspot.com.br/2011/12/partial-validation-with-data.html
 */
 
using System;
using System.Linq;
using System.Text.RegularExpressions;
using System.Web.Mvc;
 
namespace MultipleFormsInTheSameViewSample.Filters
{
    public class IgnoreModelErrorsAttribute : ActionFilterAttribute
    {
        private readonly string _keysString;
 
        public IgnoreModelErrorsAttribute(string keys)
        {
            _keysString = keys;
        }
 
        public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            var modelState = filterContext.Controller.ViewData.ModelState;
 
            var keyPatterns = _keysString.Split(new[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
 
            foreach (var t in keyPatterns)
            {
                var keyPattern = t
                    .Trim()
                    .Replace(@".", @"\.")
                    .Replace(@"[", @"\[")
                    .Replace(@"]", @"\]")
                    .Replace(@"\[\]", @"\[[0-9]+\]")
                    .Replace(@"*", @"[A-Za-z0-9]+");
                var matchingKeys = modelState.Keys.Where(x => Regex.IsMatch(x, keyPattern));
 
                foreach (var matchingKey in matchingKeys)
                    modelState[matchingKey].Errors.Clear();
            }
        }
    }
}
```

O repositório no github se encontra [aqui](https://github.com/emersonsoares/MultipleFormsInTheSameViewSample) e o demo deste projeto [aqui](http://multipleformsinthesameviewsample.apphb.com/).

Bem, dessa forma temos a validação dos formulários de forma independente e sem conflitos, e o POST ainda é feito para a respectiva Action de cada formulário.

Espero ter ajudado ;)