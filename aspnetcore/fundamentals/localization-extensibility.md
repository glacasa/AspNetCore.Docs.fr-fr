---
title: Extensibilité de la localisation
author: hishamco
description: Découvrez comment étendre les API de localisation dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: 92fe954ea6bf5d0a8f9f62f4da696d197c51af04
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776754"
---
# <a name="localization-extensibility"></a><span data-ttu-id="41361-103">Extensibilité de la localisation</span><span class="sxs-lookup"><span data-stu-id="41361-103">Localization Extensibility</span></span>

<span data-ttu-id="41361-104">Par [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="41361-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="41361-105">Cet article :</span><span class="sxs-lookup"><span data-stu-id="41361-105">This article:</span></span>

* <span data-ttu-id="41361-106">Répertorie les points d’extensibilité sur les API de localisation.</span><span class="sxs-lookup"><span data-stu-id="41361-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="41361-107">Fournit des instructions sur la façon d’étendre la localisation des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41361-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="41361-108">Points extensibles dans les API de localisation</span><span class="sxs-lookup"><span data-stu-id="41361-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="41361-109">Les API de localisation ASP.NET Core sont conçues pour être extensibles.</span><span class="sxs-lookup"><span data-stu-id="41361-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="41361-110">L’extensibilité permet aux développeurs de personnaliser la localisation en fonction de leurs besoins.</span><span class="sxs-lookup"><span data-stu-id="41361-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="41361-111">Par exemple, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) a un `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="41361-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="41361-112">`POStringLocalizer` décrit en détail l’utilisation de la [Localisation d’objet portable](xref:fundamentals/portable-object-localization) pour utiliser des fichiers `PO` afin de stocker des ressources de localisation.</span><span class="sxs-lookup"><span data-stu-id="41361-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="41361-113">Cet article répertorie les deux principaux points d’extensibilité fournis par les API de localisation :</span><span class="sxs-lookup"><span data-stu-id="41361-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="41361-114">Fournisseurs de culture de localisation</span><span class="sxs-lookup"><span data-stu-id="41361-114">Localization Culture Providers</span></span>

<span data-ttu-id="41361-115">Les API de localisation ASP.NET Core incluent quatre fournisseurs par défaut qui peuvent déterminer la culture actuelle d’une requête d’exécution :</span><span class="sxs-lookup"><span data-stu-id="41361-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="41361-116">Les fournisseurs précédents sont décrits en détail dans la documentation de l’[intergiciel de localisation](xref:fundamentals/localization).</span><span class="sxs-lookup"><span data-stu-id="41361-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="41361-117">Si les fournisseurs par défaut ne répondent pas à vos besoins, créez un fournisseur personnalisé à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="41361-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="41361-118">Utiliser CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="41361-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="41361-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> fournit un <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personnalisé qui utilise un délégué simple pour déterminer la culture de localisation actuelle :</span><span class="sxs-lookup"><span data-stu-id="41361-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
options.AddInitialRequestCultureProvider(
    new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
options.RequestCultureProviders.Insert(0, 
    new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="41361-120">Utiliser une nouvelle implémentation de RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="41361-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="41361-121">Une nouvelle implémentation de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> peut être créée pour déterminer les informations de culture de requête à partir d’une source personnalisée.</span><span class="sxs-lookup"><span data-stu-id="41361-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="41361-122">Par exemple, la source personnalisée peut être un fichier de configuration ou une base de données.</span><span class="sxs-lookup"><span data-stu-id="41361-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="41361-123">L’exemple suivant montre `AppSettingsRequestCultureProvider`, qui étend le <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> pour déterminer les informations de culture de requête à partir de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="41361-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="41361-124">Ressources de localisation</span><span class="sxs-lookup"><span data-stu-id="41361-124">Localization resources</span></span>

<span data-ttu-id="41361-125">La localisation ASP.NET Core fournit <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="41361-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="41361-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> est une implémentation de <xref:Microsoft.Extensions.Localization.IStringLocalizer> qui utilise `resx` pour stocker des ressources de localisation.</span><span class="sxs-lookup"><span data-stu-id="41361-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="41361-127">Vous n’êtes pas limité à l’utilisation de fichiers `resx`.</span><span class="sxs-lookup"><span data-stu-id="41361-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="41361-128">En implémentant `IStringLocalized`, n’importe quelle source de données peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="41361-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="41361-129">Les exemples de projets suivants implémentent <xref:Microsoft.Extensions.Localization.IStringLocalizer> :</span><span class="sxs-lookup"><span data-stu-id="41361-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="41361-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="41361-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="41361-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="41361-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="41361-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="41361-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)