---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 2e6aabda449a24496916c6ea9fcbd38062b54c04
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017452"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="61edf-103">Gérer les erreurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61edf-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="61edf-104">Par [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="61edf-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="61edf-105">Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="61edf-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="61edf-106">Consultez <xref:web-api/handle-errors> pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="61edf-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="61edf-107">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="61edf-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="61edf-108">([Comment télécharger](xref:index#how-to-download-a-sample).) L’article contient des instructions sur la façon de définir des directives de préprocesseur ( `#if` , `#endif` , `#define` ) dans l’exemple d’application pour activer différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="61edf-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="61edf-109">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="61edf-109">Developer Exception Page</span></span>

<span data-ttu-id="61edf-110">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="61edf-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="61edf-111">Elle est mise à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61edf-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="61edf-112">Ajoutez du code à la méthode `Startup.Configure` pour activer la page lorsque l’application s’exécute dans [l’environnement](xref:fundamentals/environments) de développement :</span><span class="sxs-lookup"><span data-stu-id="61edf-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="61edf-113">Placez l’appel à <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> avant tous les middlewares dont vous souhaitez intercepter les exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="61edf-114">Activez la page exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement**.</span><span class="sxs-lookup"><span data-stu-id="61edf-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="61edf-115">Il n’est pas souhaitable de partager publiquement des informations détaillées sur les exceptions quand l’application s’exécute en production.</span><span class="sxs-lookup"><span data-stu-id="61edf-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="61edf-116">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="61edf-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="61edf-117">Cette page inclut les informations suivantes sur l’exception et la demande :</span><span class="sxs-lookup"><span data-stu-id="61edf-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="61edf-118">Arborescence des appels de procédure</span><span class="sxs-lookup"><span data-stu-id="61edf-118">Stack trace</span></span>
* <span data-ttu-id="61edf-119">Paramètres de la chaîne de requête (le cas échéant)</span><span class="sxs-lookup"><span data-stu-id="61edf-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="61edf-120">Cookies (le cas échéant)</span><span class="sxs-lookup"><span data-stu-id="61edf-120">Cookies (if any)</span></span>
* <span data-ttu-id="61edf-121">En-têtes</span><span class="sxs-lookup"><span data-stu-id="61edf-121">Headers</span></span>

<span data-ttu-id="61edf-122">Pour afficher la Page d’exceptions du développeur dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez la directive de préprocesseur `DevEnvironment` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="61edf-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="61edf-123">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="61edf-123">Exception handler page</span></span>

<span data-ttu-id="61edf-124">Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="61edf-125">Le middleware :</span><span class="sxs-lookup"><span data-stu-id="61edf-125">The middleware:</span></span>

* <span data-ttu-id="61edf-126">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="61edf-127">Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e).</span><span class="sxs-lookup"><span data-stu-id="61edf-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="61edf-128">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="61edf-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="61edf-129">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="61edf-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="61edf-130">Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="61edf-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="61edf-131">Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage correspondant.</span><span class="sxs-lookup"><span data-stu-id="61edf-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="61edf-132">Voici la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="61edf-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="61edf-133">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="61edf-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="61edf-134">Les verbes explicites empêchent certaines demandes d’atteindre la méthode.</span><span class="sxs-lookup"><span data-stu-id="61edf-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="61edf-135">Autorisez l’accès anonyme à la méthode afin que les utilisateurs non authentifiés puissent recevoir la vue des erreurs.</span><span class="sxs-lookup"><span data-stu-id="61edf-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="61edf-136">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="61edf-136">Access the exception</span></span>

<span data-ttu-id="61edf-137">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="61edf-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="61edf-138">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="61edf-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="61edf-139">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="61edf-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="61edf-140">Pour afficher la page de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerPage` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="61edf-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="61edf-141">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="61edf-141">Exception handler lambda</span></span>

<span data-ttu-id="61edf-142">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>,</span><span class="sxs-lookup"><span data-stu-id="61edf-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="61edf-143">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="61edf-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="61edf-144">Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="61edf-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="61edf-145">Dans le code précédent, `await context.Response.WriteAsync(new string(' ', 512));` est ajouté afin que le navigateur Internet Explorer affiche le message d’erreur plutôt qu’un message d’erreur IE.</span><span class="sxs-lookup"><span data-stu-id="61edf-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="61edf-146">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="61edf-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="61edf-147">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="61edf-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="61edf-148">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="61edf-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="61edf-149">Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="61edf-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="61edf-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="61edf-150">UseStatusCodePages</span></span>

<span data-ttu-id="61edf-151">Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable*.</span><span class="sxs-lookup"><span data-stu-id="61edf-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="61edf-152">L’application retourne un code d’état et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="61edf-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="61edf-153">Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.</span><span class="sxs-lookup"><span data-stu-id="61edf-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="61edf-154">Le middleware est mis à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61edf-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="61edf-155">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="61edf-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="61edf-156">Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="61edf-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="61edf-157">Voici un exemple du texte affiché par les gestionnaires par défaut :</span><span class="sxs-lookup"><span data-stu-id="61edf-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="61edf-158">Pour voir l’un des formats de pages de codes d’état dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez une des directives de préprocesseur qui commencent par `StatusCodePages`, puis sélectionnez **Déclencher une erreur 404** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="61edf-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="61edf-159">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="61edf-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="61edf-160">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="61edf-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="61edf-161">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="61edf-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="61edf-162">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="61edf-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="61edf-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="61edf-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="61edf-164">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :</span><span class="sxs-lookup"><span data-stu-id="61edf-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="61edf-165">Envoie un code d’état *302 - Trouvé* au client.</span><span class="sxs-lookup"><span data-stu-id="61edf-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="61edf-166">Redirige le client à l’emplacement fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="61edf-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="61edf-167">Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="61edf-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="61edf-168">S’il commence par un tilde (~), celui-ci est remplacé par le `PathBase` de l’application.</span><span class="sxs-lookup"><span data-stu-id="61edf-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="61edf-169">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="61edf-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="61edf-170">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="61edf-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="61edf-171">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="61edf-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="61edf-172">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="61edf-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="61edf-173">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="61edf-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="61edf-174">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="61edf-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="61edf-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="61edf-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="61edf-176">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :</span><span class="sxs-lookup"><span data-stu-id="61edf-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="61edf-177">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="61edf-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="61edf-178">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="61edf-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="61edf-179">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="61edf-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="61edf-180">Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État.</span><span class="sxs-lookup"><span data-stu-id="61edf-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="61edf-181">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="61edf-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="61edf-182">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="61edf-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="61edf-183">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="61edf-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="61edf-184">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="61edf-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="61edf-185">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="61edf-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="61edf-186">Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état.</span><span class="sxs-lookup"><span data-stu-id="61edf-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="61edf-187">Le modèle d’URL doit commencer par une barre oblique (`/`).</span><span class="sxs-lookup"><span data-stu-id="61edf-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="61edf-188">Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin.</span><span class="sxs-lookup"><span data-stu-id="61edf-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="61edf-189">Par exemple, une Razor page pour les erreurs doit accepter la valeur de segment de chemin d’accès facultative avec la `@page` directive :</span><span class="sxs-lookup"><span data-stu-id="61edf-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="61edf-190">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="61edf-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="61edf-191">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="61edf-191">Disable status code pages</span></span>

<span data-ttu-id="61edf-192">Pour désactiver les pages de codes d’État pour un contrôleur MVC ou une méthode d’action, utilisez l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="61edf-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="61edf-193">Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="61edf-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="61edf-194">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="61edf-194">Exception-handling code</span></span>

<span data-ttu-id="61edf-195">Le code dans les pages de gestion des exceptions peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="61edf-196">Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.</span><span class="sxs-lookup"><span data-stu-id="61edf-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="61edf-197">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="61edf-197">Response headers</span></span>

<span data-ttu-id="61edf-198">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="61edf-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="61edf-199">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="61edf-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="61edf-200">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="61edf-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="61edf-201">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="61edf-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="61edf-202">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="61edf-202">Server exception handling</span></span>

<span data-ttu-id="61edf-203">En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="61edf-204">Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="61edf-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="61edf-205">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="61edf-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="61edf-206">Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="61edf-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="61edf-207">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="61edf-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="61edf-208">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="61edf-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="61edf-209">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="61edf-209">Startup exception handling</span></span>

<span data-ttu-id="61edf-210">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="61edf-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="61edf-211">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="61edf-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="61edf-212">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="61edf-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="61edf-213">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="61edf-213">If binding fails:</span></span>

* <span data-ttu-id="61edf-214">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="61edf-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="61edf-215">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="61edf-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="61edf-216">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="61edf-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="61edf-217">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="61edf-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="61edf-218">Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="61edf-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="61edf-219">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="61edf-219">Database error page</span></span>

<span data-ttu-id="61edf-220">L’intergiciel (middleware) de page d’erreur de base de données capture des exceptions liées à la base de données qui peuvent être résolues à l’aide de Entity Framework migrations.</span><span class="sxs-lookup"><span data-stu-id="61edf-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="61edf-221">Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée.</span><span class="sxs-lookup"><span data-stu-id="61edf-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="61edf-222">Cette page ne doit être activée que dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="61edf-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="61edf-223">Pour cela, ajoutez du code à `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="61edf-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="61edf-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>requiert le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="61edf-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="61edf-225">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="61edf-225">Exception filters</span></span>

<span data-ttu-id="61edf-226">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="61edf-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="61edf-227">Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="61edf-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="61edf-228">Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="61edf-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="61edf-229">Pour plus d'informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="61edf-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="61edf-230">Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="61edf-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="61edf-231">Nous vous recommandons d’utiliser le middleware.</span><span class="sxs-lookup"><span data-stu-id="61edf-231">We recommend using the middleware.</span></span> <span data-ttu-id="61edf-232">N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="61edf-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="61edf-233">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="61edf-233">Model state errors</span></span>

<span data-ttu-id="61edf-234">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="61edf-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61edf-235">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="61edf-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
