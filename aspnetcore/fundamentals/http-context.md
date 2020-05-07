---
title: Accéder à HttpContext dans ASP.NET Core
author: coderandhiker
description: Découvrez comment accéder à HttpContext dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: 716e74551b95455c99abbac58b712f013acfde56
ms.sourcegitcommit: d4527df91f2c15bbe1cbf5a541adbea5747897aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82852359"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="e6da5-103">Accéder à HttpContext dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6da5-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="e6da5-104">ASP.NET Core les applications `HttpContext` accessibles par <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> le biais de l’interface <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>et de son implémentation par défaut.</span><span class="sxs-lookup"><span data-stu-id="e6da5-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="e6da5-105">Il est seulement nécessaire d’utiliser `IHttpContextAccessor` quand vous avez besoin d’accéder à `HttpContext` à l’intérieur d’un service.</span><span class="sxs-lookup"><span data-stu-id="e6da5-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="e6da5-106">Utiliser HttpContext à Razor partir de pages</span><span class="sxs-lookup"><span data-stu-id="e6da5-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="e6da5-107">Les Razor pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposent <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> la propriété :</span><span class="sxs-lookup"><span data-stu-id="e6da5-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="e6da5-108">Utiliser HttpContext à partir Razor d’une vue</span><span class="sxs-lookup"><span data-stu-id="e6da5-108">Use HttpContext from a Razor view</span></span>

Razor<span data-ttu-id="e6da5-109">les vues exposent directement le `HttpContext` via une propriété [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) sur la vue.</span><span class="sxs-lookup"><span data-stu-id="e6da5-109"> views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="e6da5-110">L’exemple suivant récupère le nom d’utilisateur actuel dans une application intranet à l’aide de l’authentification Windows :</span><span class="sxs-lookup"><span data-stu-id="e6da5-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="e6da5-111">Utiliser HttpContext à partir d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="e6da5-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="e6da5-112">Les contrôleurs exposent la propriété [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="e6da5-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="e6da5-113">Utiliser HttpContext à partir d’un intergiciel</span><span class="sxs-lookup"><span data-stu-id="e6da5-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="e6da5-114">Lorsque vous travaillez avec des composants d’intergiciel personnalisés, `HttpContext` est transmis à la méthode `Invoke` ou `InvokeAsync` et accessible lorsque l’intergiciel est configuré :</span><span class="sxs-lookup"><span data-stu-id="e6da5-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="e6da5-115">Utiliser HttpContext à partir de composants personnalisés</span><span class="sxs-lookup"><span data-stu-id="e6da5-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="e6da5-116">Pour les autres infrastructures et les composants personnalisés qui requièrent l’accès à `HttpContext`, l’approche recommandée consiste à inscrire une dépendance à l’aide du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) intégré.</span><span class="sxs-lookup"><span data-stu-id="e6da5-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e6da5-117">Le conteneur `IHttpContextAccessor` d’injection de dépendances fournit à toutes les classes qui le déclarent comme une dépendance dans leurs constructeurs :</span><span class="sxs-lookup"><span data-stu-id="e6da5-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="e6da5-118">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e6da5-118">In the following example:</span></span>

* <span data-ttu-id="e6da5-119">`UserRepository` déclare sa dépendance par rapport à `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="e6da5-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="e6da5-120">Il y a dépendance lorsque l’instance de dépendances résout la chaîne de dépendance et crée une instance de `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="e6da5-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="e6da5-121">Accès de HttpContext à partir d’un thread en arrière-plan</span><span class="sxs-lookup"><span data-stu-id="e6da5-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="e6da5-122">`HttpContext`n’est pas thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e6da5-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="e6da5-123">La lecture ou l’écriture de propriétés de `HttpContext` en dehors du traitement d’une requête peut entraîner une exception <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="e6da5-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="e6da5-124">Si votre application génère des `NullReferenceException` Erreurs sporadiques, examinez les parties du code qui démarrent le traitement en arrière-plan ou qui poursuivent le traitement après la fin d’une demande.</span><span class="sxs-lookup"><span data-stu-id="e6da5-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="e6da5-125">Recherchez des erreurs, telles que la définition d’une méthode `async void`de contrôleur comme.</span><span class="sxs-lookup"><span data-stu-id="e6da5-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="e6da5-126">Pour effectuer un travail en arrière-plan en toute sécurité avec des données `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="e6da5-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="e6da5-127">Copiez les données nécessaires pendant le traitement de la requête.</span><span class="sxs-lookup"><span data-stu-id="e6da5-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="e6da5-128">Transmettez les données copiées à une tâche en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="e6da5-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="e6da5-129">Pour éviter tout code non sécurisé, ne transmettez jamais `HttpContext` à une méthode qui effectue un travail en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="e6da5-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="e6da5-130">Transmettez les données requises à la place.</span><span class="sxs-lookup"><span data-stu-id="e6da5-130">Pass the required data instead.</span></span> <span data-ttu-id="e6da5-131">Dans l’exemple suivant, `SendEmailCore` est appelé pour commencer à envoyer un message électronique.</span><span class="sxs-lookup"><span data-stu-id="e6da5-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="e6da5-132">`correlationId` Est passé à, `SendEmailCore`et non à `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e6da5-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="e6da5-133">L’exécution du `SendEmailCore` code n’attend pas la fin de :</span><span class="sxs-lookup"><span data-stu-id="e6da5-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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
```

## <a name="blazor-and-shared-state"></a>Blazor<span data-ttu-id="e6da5-134">et état partagé</span><span class="sxs-lookup"><span data-stu-id="e6da5-134"> and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
