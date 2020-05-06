---
title: Accéder à HttpContext dans ASP.NET Core
author: coderandhiker
description: Découvrez comment accéder à HttpContext dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: ac0982f144c98bf1540b16013497fabeb6683e63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773976"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Accéder à HttpContext dans ASP.NET Core

ASP.NET Core les applications `HttpContext` accessibles par <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> le biais de l’interface <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>et de son implémentation par défaut. Il est seulement nécessaire d’utiliser `IHttpContextAccessor` quand vous avez besoin d’accéder à `HttpContext` à l’intérieur d’un service.

## <a name="use-httpcontext-from-razor-pages"></a>Utiliser HttpContext à Razor partir de pages

Les Razor pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposent <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> la propriété :

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

## <a name="use-httpcontext-from-a-razor-view"></a>Utiliser HttpContext à partir Razor d’une vue

Razorles vues exposent directement le `HttpContext` via une propriété [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) sur la vue. L’exemple suivant récupère le nom d’utilisateur actuel dans une application intranet à l’aide de l’authentification Windows :

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

Pour les autres infrastructures et les composants personnalisés qui requièrent l’accès à `HttpContext`, l’approche recommandée consiste à inscrire une dépendance à l’aide du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) intégré. Le conteneur `IHttpContextAccessor` d’injection de dépendances fournit à toutes les classes qui le déclarent comme une dépendance dans leurs constructeurs :

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

`HttpContext`n’est pas thread-safe. La lecture ou l’écriture de propriétés de `HttpContext` en dehors du traitement d’une requête peut entraîner une exception <xref:System.NullReferenceException>.

> [!NOTE]
> Si votre application génère des `NullReferenceException` Erreurs sporadiques, examinez les parties du code qui démarrent le traitement en arrière-plan ou qui poursuivent le traitement après la fin d’une demande. Recherchez des erreurs, telles que la définition d’une méthode `async void`de contrôleur comme.

Pour effectuer un travail en arrière-plan en toute sécurité avec des données `HttpContext` :

* Copiez les données nécessaires pendant le traitement de la requête.
* Transmettez les données copiées à une tâche en arrière-plan.

Pour éviter tout code non sécurisé, ne transmettez jamais `HttpContext` à une méthode qui effectue un travail en arrière-plan. Transmettez les données requises à la place. Dans l’exemple suivant, `SendEmailCore` est appelé pour commencer à envoyer un message électronique. `correlationId` Est passé à, `SendEmailCore`et non à `HttpContext`. L’exécution du `SendEmailCore` code n’attend pas la fin de :

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
