---
title: Client IP safelier pour ASP.NET Core
author: damienbod
description: Découvrez comment écrire des filtres d’intergiciel ou d’action pour valider des adresses IP distantes par rapport à une liste d’adresses IP approuvées.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 621be5351acb251335a42f57e8ea670af1b35a87
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634447"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="2976f-103">Client IP safelier pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2976f-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="2976f-104">Par [Damien Bowden](https://twitter.com/damien_bod) et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="2976f-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="2976f-105">Cet article présente trois façons d’implémenter une adresse IP safelit (également appelée « liste verte ») dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2976f-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="2976f-106">Un exemple d’application associé illustre les trois approches.</span><span class="sxs-lookup"><span data-stu-id="2976f-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="2976f-107">Vous pouvez utiliser :</span><span class="sxs-lookup"><span data-stu-id="2976f-107">You can use:</span></span>

* <span data-ttu-id="2976f-108">Intergiciel pour vérifier l’adresse IP distante de chaque demande.</span><span class="sxs-lookup"><span data-stu-id="2976f-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="2976f-109">Filtres d’action MVC pour vérifier l’adresse IP distante des demandes pour des contrôleurs ou des méthodes d’action spécifiques.</span><span class="sxs-lookup"><span data-stu-id="2976f-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="2976f-110">Razor Pages filtres pour vérifier l’adresse IP distante des demandes de Razor pages.</span><span class="sxs-lookup"><span data-stu-id="2976f-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="2976f-111">Dans chaque cas, une chaîne contenant des adresses IP clientes approuvées est stockée dans un paramètre d’application.</span><span class="sxs-lookup"><span data-stu-id="2976f-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="2976f-112">Le middleware ou le filtre :</span><span class="sxs-lookup"><span data-stu-id="2976f-112">The middleware or filter:</span></span>

* <span data-ttu-id="2976f-113">Analyse la chaîne dans un tableau.</span><span class="sxs-lookup"><span data-stu-id="2976f-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="2976f-114">Vérifie si l’adresse IP distante existe dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="2976f-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="2976f-115">L’accès est autorisé si le tableau contient l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="2976f-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="2976f-116">Dans le cas contraire, un code d’état HTTP 403 interdit est retourné.</span><span class="sxs-lookup"><span data-stu-id="2976f-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="2976f-117">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2976f-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="2976f-118">Adresse IP safelit</span><span class="sxs-lookup"><span data-stu-id="2976f-118">IP address safelist</span></span>

<span data-ttu-id="2976f-119">Dans l’exemple d’application, l’adresse IP safelit est :</span><span class="sxs-lookup"><span data-stu-id="2976f-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="2976f-120">Défini par la `AdminSafeList` propriété dans le fichier *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="2976f-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="2976f-121">Chaîne délimitée par des points-virgules qui peut contenir des adresses [IPv4 (Internet Protocol version 4)](https://wikipedia.org/wiki/IPv4) et [IPv6 (Internet Protocol version 6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="2976f-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="2976f-122">Dans l’exemple précédent, les adresses IPv4 de `127.0.0.1` et `192.168.1.5` et l’adresse IPv6 de bouclage `::1` (format compressé pour `0:0:0:0:0:0:0:1` ) sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="2976f-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="2976f-123">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="2976f-123">Middleware</span></span>

<span data-ttu-id="2976f-124">La `Startup.Configure` méthode ajoute le `AdminSafeListMiddleware` type d’intergiciel (middleware) personnalisé au pipeline de demande de l’application.</span><span class="sxs-lookup"><span data-stu-id="2976f-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="2976f-125">L’offre safelit est récupérée avec le fournisseur de configuration .NET Core et transmise en tant que paramètre de constructeur.</span><span class="sxs-lookup"><span data-stu-id="2976f-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="2976f-126">L’intergiciel (middleware) analyse la chaîne dans un tableau et recherche l’adresse IP distante dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="2976f-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="2976f-127">Si l’adresse IP distante est introuvable, l’intergiciel (middleware) renvoie HTTP 403 interdit.</span><span class="sxs-lookup"><span data-stu-id="2976f-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="2976f-128">Ce processus de validation est contourné pour les requêtes HTTP d’extraction.</span><span class="sxs-lookup"><span data-stu-id="2976f-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="2976f-129">Filtre d’action</span><span class="sxs-lookup"><span data-stu-id="2976f-129">Action filter</span></span>

<span data-ttu-id="2976f-130">Si vous souhaitez un contrôle d’accès piloté par safeli pour des contrôleurs MVC ou des méthodes d’action spécifiques, utilisez un filtre d’action.</span><span class="sxs-lookup"><span data-stu-id="2976f-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="2976f-131">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2976f-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="2976f-132">Dans `Startup.ConfigureServices` , ajoutez le filtre d’action à la collection de filtres Mvc.</span><span class="sxs-lookup"><span data-stu-id="2976f-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="2976f-133">Dans l’exemple suivant, un `ClientIpCheckActionFilter` filtre d’action est ajouté.</span><span class="sxs-lookup"><span data-stu-id="2976f-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="2976f-134">Un safelit et une instance d’enregistreur d’événements de console sont passés en tant que paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="2976f-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="2976f-135">Le filtre d’action peut ensuite être appliqué à un contrôleur ou à une méthode d’action avec l’attribut [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="2976f-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="2976f-136">Dans l’exemple d’application, le filtre d’action est appliqué à la `Get` méthode d’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2976f-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="2976f-137">Lorsque vous testez l’application en envoyant :</span><span class="sxs-lookup"><span data-stu-id="2976f-137">When you test the app by sending:</span></span>

* <span data-ttu-id="2976f-138">Une requête HTTP d’extraction, l' `[ServiceFilter]` attribut valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="2976f-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="2976f-139">Si l’accès est autorisé à la `Get` méthode d’action, une variante de la sortie de console suivante est générée par le filtre d’action et la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="2976f-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="2976f-140">Un verbe de requête HTTP autre que obtenir, l' `AdminSafeListMiddleware` intergiciel (middleware) valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="2976f-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="2976f-141">Razor Filtre de pages</span><span class="sxs-lookup"><span data-stu-id="2976f-141">Razor Pages filter</span></span>

<span data-ttu-id="2976f-142">Si vous souhaitez un contrôle d’accès piloté par safelis pour une Razor application pages, utilisez un Razor filtre pages.</span><span class="sxs-lookup"><span data-stu-id="2976f-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="2976f-143">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2976f-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="2976f-144">Dans `Startup.ConfigureServices` , activez le Razor filtre pages en l’ajoutant à la collection de filtres Mvc.</span><span class="sxs-lookup"><span data-stu-id="2976f-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="2976f-145">Dans l’exemple suivant, un `ClientIpCheckPageFilter` Razor filtre de pages est ajouté.</span><span class="sxs-lookup"><span data-stu-id="2976f-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="2976f-146">Un safelit et une instance d’enregistreur d’événements de console sont passés en tant que paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="2976f-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="2976f-147">Quand la page d' *index* de l’exemple d’application Razor est demandée, le Razor filtre de pages valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="2976f-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="2976f-148">Le filtre produit une variante de la sortie de console suivante :</span><span class="sxs-lookup"><span data-stu-id="2976f-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="2976f-149">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="2976f-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="2976f-150">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="2976f-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
