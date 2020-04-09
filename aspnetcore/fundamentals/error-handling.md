---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658815"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="75ea6-103">Gérer les erreurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75ea6-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="75ea6-104">Par [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="75ea6-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="75ea6-105">Cet article couvre les approches courantes de gestion des erreurs dans ASP.NET applications Web Core.</span><span class="sxs-lookup"><span data-stu-id="75ea6-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="75ea6-106">Voir <xref:web-api/handle-errors> pour les API web.</span><span class="sxs-lookup"><span data-stu-id="75ea6-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="75ea6-107">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="75ea6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="75ea6-108">([Comment télécharger](xref:index#how-to-download-a-sample).) L’article comprend des instructions sur la`#if` `#endif`façon `#define`de définir les directives de préprocesseur ( , , ) dans l’application d’échantillon pour activer différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="75ea6-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="75ea6-109">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="75ea6-109">Developer Exception Page</span></span>

<span data-ttu-id="75ea6-110">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="75ea6-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="75ea6-111">Elle est mise à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="75ea6-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="75ea6-112">Ajoutez du code à la méthode `Startup.Configure` pour activer la page lorsque l’application s’exécute dans [l’environnement](xref:fundamentals/environments) de développement :</span><span class="sxs-lookup"><span data-stu-id="75ea6-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="75ea6-113">Placez l’appel à <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> avant tous les middlewares dont vous souhaitez intercepter les exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="75ea6-114">Activez la page d’exception des développeurs **uniquement lorsque l’application est en cours d’exécution dans l’environnement de développement**.</span><span class="sxs-lookup"><span data-stu-id="75ea6-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="75ea6-115">Il n’est pas souhaitable de partager publiquement des informations détaillées sur les exceptions quand l’application s’exécute en production.</span><span class="sxs-lookup"><span data-stu-id="75ea6-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="75ea6-116">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="75ea6-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="75ea6-117">Cette page inclut les informations suivantes sur l’exception et la demande :</span><span class="sxs-lookup"><span data-stu-id="75ea6-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="75ea6-118">Arborescence des appels de procédure</span><span class="sxs-lookup"><span data-stu-id="75ea6-118">Stack trace</span></span>
* <span data-ttu-id="75ea6-119">Paramètres de la chaîne de requête (le cas échéant)</span><span class="sxs-lookup"><span data-stu-id="75ea6-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="75ea6-120">Cookies (le cas échéant)</span><span class="sxs-lookup"><span data-stu-id="75ea6-120">Cookies (if any)</span></span>
* <span data-ttu-id="75ea6-121">headers</span><span class="sxs-lookup"><span data-stu-id="75ea6-121">Headers</span></span>

<span data-ttu-id="75ea6-122">Pour afficher la Page d’exceptions du développeur dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez la directive de préprocesseur `DevEnvironment` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="75ea6-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="75ea6-123">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="75ea6-123">Exception handler page</span></span>

<span data-ttu-id="75ea6-124">Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="75ea6-125">Le middleware :</span><span class="sxs-lookup"><span data-stu-id="75ea6-125">The middleware:</span></span>

* <span data-ttu-id="75ea6-126">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="75ea6-127">Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e).</span><span class="sxs-lookup"><span data-stu-id="75ea6-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="75ea6-128">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="75ea6-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="75ea6-129">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="75ea6-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="75ea6-130">Le modèle d’application Razor Pages fournit une page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> d’erreur (*.cshtml*) et classe (`ErrorModel`) dans le dossier *Pages.*</span><span class="sxs-lookup"><span data-stu-id="75ea6-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="75ea6-131">Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage correspondant.</span><span class="sxs-lookup"><span data-stu-id="75ea6-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="75ea6-132">Voici la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="75ea6-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="75ea6-133">Ne marquez pas la méthode d’action du gestionnaire `HttpGet`d’erreurs avec des attributs de méthode HTTP, tels que .</span><span class="sxs-lookup"><span data-stu-id="75ea6-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="75ea6-134">Les verbes explicites empêchent certaines demandes d’atteindre la méthode.</span><span class="sxs-lookup"><span data-stu-id="75ea6-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="75ea6-135">Autorisez l’accès anonyme à la méthode afin que les utilisateurs non authentifiés puissent recevoir la vue des erreurs.</span><span class="sxs-lookup"><span data-stu-id="75ea6-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="75ea6-136">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="75ea6-136">Access the exception</span></span>

<span data-ttu-id="75ea6-137">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="75ea6-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="75ea6-138">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="75ea6-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="75ea6-139">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="75ea6-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="75ea6-140">Pour afficher la page de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerPage` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="75ea6-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="75ea6-141">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="75ea6-141">Exception handler lambda</span></span>

<span data-ttu-id="75ea6-142">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>,</span><span class="sxs-lookup"><span data-stu-id="75ea6-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="75ea6-143">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="75ea6-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="75ea6-144">Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="75ea6-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="75ea6-145">Dans le code `await context.Response.WriteAsync(new string(' ', 512));` précédent, est ajouté de sorte que le navigateur Internet Explorer affiche le message d’erreur plutôt que d’un message d’erreur IE.</span><span class="sxs-lookup"><span data-stu-id="75ea6-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="75ea6-146">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="75ea6-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="75ea6-147">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="75ea6-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="75ea6-148">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="75ea6-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="75ea6-149">Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="75ea6-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="75ea6-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="75ea6-150">UseStatusCodePages</span></span>

<span data-ttu-id="75ea6-151">Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable*.</span><span class="sxs-lookup"><span data-stu-id="75ea6-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="75ea6-152">L’application retourne un code d’état et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="75ea6-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="75ea6-153">Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.</span><span class="sxs-lookup"><span data-stu-id="75ea6-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="75ea6-154">Le middleware est mis à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="75ea6-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="75ea6-155">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="75ea6-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="75ea6-156">Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="75ea6-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="75ea6-157">Voici un exemple du texte affiché par les gestionnaires par défaut :</span><span class="sxs-lookup"><span data-stu-id="75ea6-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="75ea6-158">Pour voir l’un des formats de pages de codes d’état dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez une des directives de préprocesseur qui commencent par `StatusCodePages`, puis sélectionnez **Déclencher une erreur 404** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="75ea6-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="75ea6-159">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="75ea6-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="75ea6-160">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="75ea6-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="75ea6-161">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="75ea6-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="75ea6-162">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="75ea6-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="75ea6-163">UtiliserStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="75ea6-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="75ea6-164">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> :</span><span class="sxs-lookup"><span data-stu-id="75ea6-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="75ea6-165">Envoie un code d’état *302 - Trouvé* au client.</span><span class="sxs-lookup"><span data-stu-id="75ea6-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="75ea6-166">Redirige le client à l’emplacement fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="75ea6-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="75ea6-167">Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="75ea6-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="75ea6-168">S’il commence par un tilde (~), celui-ci est remplacé par le `PathBase` de l’application.</span><span class="sxs-lookup"><span data-stu-id="75ea6-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="75ea6-169">Si vous pointez vers un point de terminaison dans l’application, créez une vue MVC ou une page Razor pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="75ea6-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="75ea6-170">Pour consulter un exemple Razor Pages, voir *Pages/StatusCode.cshtml* dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="75ea6-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="75ea6-171">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="75ea6-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="75ea6-172">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="75ea6-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="75ea6-173">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="75ea6-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="75ea6-174">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="75ea6-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="75ea6-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="75ea6-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="75ea6-176">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> :</span><span class="sxs-lookup"><span data-stu-id="75ea6-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="75ea6-177">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="75ea6-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="75ea6-178">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="75ea6-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="75ea6-179">Si vous pointez vers un point de terminaison dans l’application, créez une vue MVC ou une page Razor pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="75ea6-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="75ea6-180">Pour consulter un exemple Razor Pages, voir *Pages/StatusCode.cshtml* dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="75ea6-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="75ea6-181">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="75ea6-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="75ea6-182">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="75ea6-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="75ea6-183">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="75ea6-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="75ea6-184">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="75ea6-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="75ea6-185">Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état.</span><span class="sxs-lookup"><span data-stu-id="75ea6-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="75ea6-186">Le modèle d’URL doit commencer par une barre oblique (`/`).</span><span class="sxs-lookup"><span data-stu-id="75ea6-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="75ea6-187">Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin.</span><span class="sxs-lookup"><span data-stu-id="75ea6-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="75ea6-188">Par exemple, une Razor Page pour les erreurs doit accepter la valeur du segment de chemin d’accès facultatif avec la directive `@page` :</span><span class="sxs-lookup"><span data-stu-id="75ea6-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="75ea6-189">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="75ea6-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="75ea6-190">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="75ea6-190">Disable status code pages</span></span>

<span data-ttu-id="75ea6-191">Pour désactiver les pages de code d’état d’un contrôleur ou d’une méthode d’action MVC, utilisez l’attribut. [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute)</span><span class="sxs-lookup"><span data-stu-id="75ea6-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="75ea6-192">Pour désactiver les pages de codes d’état de requêtes spécifiques dans une méthode de gestionnaire Razor Pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="75ea6-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="75ea6-193">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="75ea6-193">Exception-handling code</span></span>

<span data-ttu-id="75ea6-194">Le code dans les pages de gestion des exceptions peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="75ea6-195">Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.</span><span class="sxs-lookup"><span data-stu-id="75ea6-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="75ea6-196">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="75ea6-196">Response headers</span></span>

<span data-ttu-id="75ea6-197">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="75ea6-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="75ea6-198">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="75ea6-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="75ea6-199">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="75ea6-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="75ea6-200">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="75ea6-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="75ea6-201">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="75ea6-201">Server exception handling</span></span>

<span data-ttu-id="75ea6-202">En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="75ea6-203">Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="75ea6-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="75ea6-204">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="75ea6-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="75ea6-205">Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="75ea6-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="75ea6-206">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="75ea6-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="75ea6-207">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="75ea6-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="75ea6-208">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="75ea6-208">Startup exception handling</span></span>

<span data-ttu-id="75ea6-209">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="75ea6-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="75ea6-210">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="75ea6-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="75ea6-211">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="75ea6-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="75ea6-212">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="75ea6-212">If binding fails:</span></span>

* <span data-ttu-id="75ea6-213">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="75ea6-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="75ea6-214">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="75ea6-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="75ea6-215">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="75ea6-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="75ea6-216">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="75ea6-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="75ea6-217">Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="75ea6-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="75ea6-218">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="75ea6-218">Database error page</span></span>

<span data-ttu-id="75ea6-219">Page d’erreur de base de données Middleware capture les exceptions liées à la base de données qui peuvent être résolues en utilisant les migrations Du Cadre d’entité.</span><span class="sxs-lookup"><span data-stu-id="75ea6-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="75ea6-220">Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée.</span><span class="sxs-lookup"><span data-stu-id="75ea6-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="75ea6-221">Cette page ne doit être activée que dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="75ea6-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="75ea6-222">Pour cela, ajoutez du code à `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="75ea6-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="75ea6-223">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="75ea6-223">Exception filters</span></span>

<span data-ttu-id="75ea6-224">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="75ea6-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="75ea6-225">Dans les applications Razor Pages, ils peuvent être configurés globalement ou modèle de page par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="75ea6-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="75ea6-226">Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="75ea6-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="75ea6-227">Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="75ea6-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="75ea6-228">Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="75ea6-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="75ea6-229">Nous vous recommandons d’utiliser le middleware.</span><span class="sxs-lookup"><span data-stu-id="75ea6-229">We recommend using the middleware.</span></span> <span data-ttu-id="75ea6-230">N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="75ea6-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="75ea6-231">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="75ea6-231">Model state errors</span></span>

<span data-ttu-id="75ea6-232">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="75ea6-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75ea6-233">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="75ea6-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
