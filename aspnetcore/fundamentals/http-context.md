---
title: Accéder à HttpContext dans ASP.NET Core
author: coderandhiker
description: Découvrez comment accéder à HttpContext dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658745"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Accéder à HttpContext dans ASP.NET Core

ASP.NET les applications Core `HttpContext` accèdent par <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>l’interface et sa mise en œuvre par défaut . Il est seulement nécessaire d’utiliser `IHttpContextAccessor` quand vous avez besoin d’accéder à `HttpContext` à l’intérieur d’un service.

## <a name="use-httpcontext-from-razor-pages"></a>Utiliser HttpContext à partir de Razor Pages

Les Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> exposent la propriété :

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-razor-view"></a>Utiliser HttpContext à partir d’une vue Razor

Les vues Razor exposent `HttpContext` directement par l’intermédiaire d’une propriété [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) de la vue. L’exemple suivant récupère le nom d’utilisateur actuel dans une application intranet à l’aide de Windows Authentication :

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Utiliser HttpContext à partir d’un contrôleur

Les contrôleurs exposent la propriété [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Utiliser HttpContext à partir d’un intergiciel

Lorsque vous travaillez avec des composants d’intergiciel personnalisés, `HttpContext` est transmis à la méthode `Invoke` ou `InvokeAsync` et accessible lorsque l’intergiciel est configuré :

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Utiliser HttpContext à partir de composants personnalisés

Pour les autres infrastructures et les composants personnalisés qui requièrent l’accès à `HttpContext`, l’approche recommandée consiste à inscrire une dépendance à l’aide du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) intégré. Le conteneur d’injection de dépendance fournit le `IHttpContextAccessor` à toutes les classes qui le déclarent comme une dépendance dans leurs constructeurs:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

Dans l’exemple suivant :

* `UserRepository` déclare sa dépendance par rapport à `IHttpContextAccessor`.
* Il y a dépendance lorsque l’instance de dépendances résout la chaîne de dépendance et crée une instance de `UserRepository`.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>Accès de HttpContext à partir d’un thread en arrière-plan

`HttpContext`n’est pas sans fil. La lecture ou l’écriture de propriétés de `HttpContext` en dehors du traitement d’une requête peut entraîner une exception <xref:System.NullReferenceException>.

> [!NOTE]
> Si votre application `NullReferenceException` génère des erreurs sporadiques, examinez les parties du code qui démarrent le traitement de fond ou qui continuent le traitement après la fin d’une demande. Recherchez des erreurs, telles que `async void`la définition d’une méthode de contrôleur comme .

Pour effectuer un travail en arrière-plan en toute sécurité avec des données `HttpContext` :

* Copiez les données nécessaires pendant le traitement de la requête.
* Transmettez les données copiées à une tâche en arrière-plan.

Pour éviter le code dangereux, ne transmettez jamais la méthode qui effectue le `HttpContext` travail d’arrière-plan. Passez plutôt les données requises. Dans l’exemple `SendEmailCore` suivant, est appelé pour commencer à envoyer un e-mail. Le `correlationId` est `SendEmailCore`passé à `HttpContext`, pas le . L’exécution du code `SendEmailCore` n’attend pas la fin :

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
