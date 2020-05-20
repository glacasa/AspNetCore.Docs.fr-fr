---
<span data-ttu-id="11706-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="11706-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="11706-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="11706-102">'Blazor'</span></span>
- <span data-ttu-id="11706-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="11706-103">'Identity'</span></span>
- <span data-ttu-id="11706-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="11706-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="11706-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="11706-105">'Razor'</span></span>
- <span data-ttu-id="11706-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="11706-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="11706-107">Écrire un intergiciel (middleware) ASP.NET Core personnalisé</span><span class="sxs-lookup"><span data-stu-id="11706-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="11706-108">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="11706-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="11706-109">Un middleware est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses.</span><span class="sxs-lookup"><span data-stu-id="11706-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="11706-110">Même si ASP.NET Core propose une large palette de composants d’intergiciel (middleware) intégrés, dans certains scénarios, vous voudrez certainement écrire un intergiciel personnalisé.</span><span class="sxs-lookup"><span data-stu-id="11706-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="11706-111">Cette rubrique explique comment écrire *un intergiciel (middleware) basé sur des conventions* .</span><span class="sxs-lookup"><span data-stu-id="11706-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="11706-112">Pour une approche qui utilise le typage fort et l’activation par demande, consultez <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="11706-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="11706-113">Classe d’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="11706-113">Middleware class</span></span>

<span data-ttu-id="11706-114">Les intergiciels sont généralement encapsulés dans une classe et exposés avec une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="11706-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="11706-115">Prenez en compte le middleware suivant, qui définit la culture de la requête actuelle à partir d’une chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="11706-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="11706-116">L’exemple de code précédent est utilisé pour illustrer la création d’un composant de middleware.</span><span class="sxs-lookup"><span data-stu-id="11706-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="11706-117">Pour plus d’informations sur la prise en charge intégrée de la localisation par ASP.NET Core, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="11706-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="11706-118">Testez l’intergiciel en transmettant la culture.</span><span class="sxs-lookup"><span data-stu-id="11706-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="11706-119">Par exemple, demandez `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="11706-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="11706-120">Le code suivant déplace le délégué de l’intergiciel dans une classe :</span><span class="sxs-lookup"><span data-stu-id="11706-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="11706-121">La classe d’intergiciel (middleware) doit inclure :</span><span class="sxs-lookup"><span data-stu-id="11706-121">The middleware class must include:</span></span>

* <span data-ttu-id="11706-122">Un constructeur public avec un paramètre de type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="11706-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="11706-123">Une méthode publique nommée `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="11706-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="11706-124">Cette méthode doit :</span><span class="sxs-lookup"><span data-stu-id="11706-124">This method must:</span></span>
  * <span data-ttu-id="11706-125">Retournez une `Task`.</span><span class="sxs-lookup"><span data-stu-id="11706-125">Return a `Task`.</span></span>
  * <span data-ttu-id="11706-126">Accepter un premier paramètre de type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="11706-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="11706-127">Les paramètres supplémentaires pour le constructeur et `Invoke`/`InvokeAsync` sont remplis par [Injection de dépendance (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="11706-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="11706-128">Dépendances de l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="11706-128">Middleware dependencies</span></span>

<span data-ttu-id="11706-129">L’intergiciel doit suivre le [principe de dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) en exposant ses dépendances dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="11706-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="11706-130">L’intergiciel est construit une fois par *durée de vie d’application*.</span><span class="sxs-lookup"><span data-stu-id="11706-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="11706-131">Consultez la section [Dépendances des middlewares par requête](#per-request-middleware-dependencies) si vous avez besoin de partager des services avec un middleware au sein d’une requête.</span><span class="sxs-lookup"><span data-stu-id="11706-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="11706-132">Les composants de middleware peuvent résoudre leurs dépendances à partir de l’[injection de dépendances](xref:fundamentals/dependency-injection) à l’aide des paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="11706-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="11706-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) peut également accepter des paramètres supplémentaires directement.</span><span class="sxs-lookup"><span data-stu-id="11706-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="11706-134">Dépendances de l’intergiciel (middleware) par requête</span><span class="sxs-lookup"><span data-stu-id="11706-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="11706-135">Étant donné que le middleware est construit au démarrage de l’application, et non par requête, les services de durée de vie *délimités* utilisés par les constructeurs du middleware ne sont pas partagés avec d’autres types injectés par des dépendances lors de chaque requête.</span><span class="sxs-lookup"><span data-stu-id="11706-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="11706-136">Si vous devez partager un service *délimité* entre votre intergiciel et d’autres types, ajoutez-le à la signature de la méthode `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="11706-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="11706-137">La méthode `Invoke` peut accepter des paramètres supplémentaires qui sont renseignés par injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="11706-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="11706-138">Méthode d’extension d’intergiciel</span><span class="sxs-lookup"><span data-stu-id="11706-138">Middleware extension method</span></span>

<span data-ttu-id="11706-139">La méthode d’extension suivante expose le middleware par le biais de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :</span><span class="sxs-lookup"><span data-stu-id="11706-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="11706-140">Le code suivant appelle l’intergiciel à partir de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="11706-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="11706-141">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="11706-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
