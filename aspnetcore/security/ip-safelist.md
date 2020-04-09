---
title: Liste de sécurité IP client pour ASP.NET Core
author: damienbod
description: Découvrez comment écrire des middleware ou des filtres d’action pour valider les adresses IP à distance par rapport à une liste d’adresses IP approuvées.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471801"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="d1e28-103">Liste de sécurité IP client pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1e28-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="d1e28-104">Par [Damien Bowden](https://twitter.com/damien_bod) et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d1e28-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="d1e28-105">Cet article montre trois façons de mettre en œuvre une liste de sécurité d’adresse IP (également connue sous le nom de liste d’autorisation) dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1e28-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="d1e28-106">Une application d’échantillon d’accompagnement démontre les trois approches.</span><span class="sxs-lookup"><span data-stu-id="d1e28-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="d1e28-107">Vous pouvez utiliser :</span><span class="sxs-lookup"><span data-stu-id="d1e28-107">You can use:</span></span>

* <span data-ttu-id="d1e28-108">Middleware pour vérifier l’adresse IP à distance de chaque demande.</span><span class="sxs-lookup"><span data-stu-id="d1e28-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="d1e28-109">Filtres d’action MVC pour vérifier l’adresse IP à distance des demandes de contrôleurs spécifiques ou méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="d1e28-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="d1e28-110">Razor Pages filtre pour vérifier l’adresse IP à distance des demandes de pages Razor.</span><span class="sxs-lookup"><span data-stu-id="d1e28-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="d1e28-111">Dans chaque cas, une chaîne contenant des adresses IP client approuvées est stockée dans un paramètre d’application.</span><span class="sxs-lookup"><span data-stu-id="d1e28-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="d1e28-112">Le middleware ou le filtre :</span><span class="sxs-lookup"><span data-stu-id="d1e28-112">The middleware or filter:</span></span>

* <span data-ttu-id="d1e28-113">Parse la ficelle dans un tableau.</span><span class="sxs-lookup"><span data-stu-id="d1e28-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="d1e28-114">Vérifie si l’adresse IP à distance existe dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="d1e28-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="d1e28-115">L’accès est autorisé si le tableau contient l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="d1e28-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="d1e28-116">Dans le cas contraire, un code de statut HTTP 403 Interdit est retourné.</span><span class="sxs-lookup"><span data-stu-id="d1e28-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="d1e28-117">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1e28-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="d1e28-118">Liste de sécurité de l’adresse IP</span><span class="sxs-lookup"><span data-stu-id="d1e28-118">IP address safelist</span></span>

<span data-ttu-id="d1e28-119">Dans l’application de l’échantillon, la liste de sécurité de l’adresse IP est :</span><span class="sxs-lookup"><span data-stu-id="d1e28-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="d1e28-120">Défini par `AdminSafeList` la propriété dans le fichier *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="d1e28-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d1e28-121">Une chaîne semi-alignée qui peut contenir à la fois [la version 4 du protocole Internet (IPv4)](https://wikipedia.org/wiki/IPv4) et [la version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) du protocole Internet.</span><span class="sxs-lookup"><span data-stu-id="d1e28-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="d1e28-122">Dans l’exemple précédent, les `127.0.0.1` adresses IPv4 de `192.168.1.5` et `::1` l’adresse `0:0:0:0:0:0:0:1`IPv6 loopback de (format compressé pour ) sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="d1e28-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="d1e28-123">Middlewares</span><span class="sxs-lookup"><span data-stu-id="d1e28-123">Middleware</span></span>

<span data-ttu-id="d1e28-124">La `Startup.Configure` méthode ajoute `AdminSafeListMiddleware` le type de middleware personnalisé au pipeline de demande de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1e28-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="d1e28-125">La liste de sécurité est récupérée avec le fournisseur de configuration .NET Core et est passée comme paramètre constructeur.</span><span class="sxs-lookup"><span data-stu-id="d1e28-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="d1e28-126">Le middleware analyse la chaîne dans un tableau et recherche l’adresse IP à distance dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="d1e28-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="d1e28-127">Si l’adresse IP à distance n’est pas trouvée, le middleware renvoie HTTP 403 Interdit.</span><span class="sxs-lookup"><span data-stu-id="d1e28-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="d1e28-128">Ce processus de validation est contourné pour les demandes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="d1e28-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="d1e28-129">Filtre d’action</span><span class="sxs-lookup"><span data-stu-id="d1e28-129">Action filter</span></span>

<span data-ttu-id="d1e28-130">Si vous souhaitez un contrôle d’accès piloté par une liste de sécurité pour des contrôleurs MVC spécifiques ou des méthodes d’action, utilisez un filtre d’action.</span><span class="sxs-lookup"><span data-stu-id="d1e28-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="d1e28-131">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1e28-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="d1e28-132">Ajouter `Startup.ConfigureServices`le filtre d’action à la collection de filtres MVC.</span><span class="sxs-lookup"><span data-stu-id="d1e28-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="d1e28-133">Dans l’exemple `ClientIpCheckActionFilter` suivant, un filtre d’action est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d1e28-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="d1e28-134">Une liste de sécurité et une instance d’enregistreur de console sont passées comme paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="d1e28-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="d1e28-135">Le filtre d’action peut ensuite être appliqué à un contrôleur ou à une méthode d’action avec l’attribut [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d1e28-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="d1e28-136">Dans l’application d’échantillon, le filtre `Get` d’action est appliqué à la méthode d’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d1e28-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="d1e28-137">Lorsque vous testez l’application en envoyant :</span><span class="sxs-lookup"><span data-stu-id="d1e28-137">When you test the app by sending:</span></span>

* <span data-ttu-id="d1e28-138">Une demande HTTP `[ServiceFilter]` GET, l’attribut valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="d1e28-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="d1e28-139">Si l’accès `Get` est autorisé à la méthode d’action, une variation de la sortie de la console suivante est produite par le filtre d’action et la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="d1e28-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="d1e28-140">Un verbe de demande `AdminSafeListMiddleware` HTTP autre que GET, le middleware valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="d1e28-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="d1e28-141">Filtre De pages de rasoir</span><span class="sxs-lookup"><span data-stu-id="d1e28-141">Razor Pages filter</span></span>

<span data-ttu-id="d1e28-142">Si vous souhaitez un contrôle d’accès safelist pour une application Razor Pages, utilisez un filtre Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d1e28-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="d1e28-143">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1e28-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="d1e28-144">Dans `Startup.ConfigureServices`, activez le filtre Razor Pages en l’ajoutant à la collection de filtres MVC.</span><span class="sxs-lookup"><span data-stu-id="d1e28-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="d1e28-145">Dans l’exemple `ClientIpCheckPageFilter` suivant, un filtre Razor Pages est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d1e28-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="d1e28-146">Une liste de sécurité et une instance d’enregistreur de console sont passées comme paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="d1e28-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="d1e28-147">Lorsque la page *Razor Index* de l’application de l’échantillon est demandée, le filtre Razor Pages valide l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="d1e28-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="d1e28-148">Le filtre produit une variation de la sortie de la console suivante :</span><span class="sxs-lookup"><span data-stu-id="d1e28-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="d1e28-149">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d1e28-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="d1e28-150">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="d1e28-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
