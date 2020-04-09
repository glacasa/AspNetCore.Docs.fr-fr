---
title: Globalisation et localisation dans ASP.NET Core
author: rick-anderson
description: Découvrez les services et intergiciels (middleware) fournis par ASP.NET Core pour localiser du contenu dans différentes langues et cultures.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: b175354220a8a71c029e005f27443d5a72749a11
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662119"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="92cdc-103">Globalisation et localisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92cdc-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="92cdc-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="92cdc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="92cdc-105">Créer un site web multilingue avec ASP.NET Core permet d’atteindre un plus large public.</span><span class="sxs-lookup"><span data-stu-id="92cdc-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="92cdc-106">ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.</span><span class="sxs-lookup"><span data-stu-id="92cdc-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="92cdc-107">L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="92cdc-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="92cdc-108">La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes.</span><span class="sxs-lookup"><span data-stu-id="92cdc-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="92cdc-109">La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="92cdc-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="92cdc-110">La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="92cdc-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="92cdc-111">Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.</span><span class="sxs-lookup"><span data-stu-id="92cdc-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="92cdc-112">La localisation d’une application implique les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="92cdc-112">App localization involves the following:</span></span>

1. <span data-ttu-id="92cdc-113">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="92cdc-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="92cdc-114">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="92cdc-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="92cdc-115">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="92cdc-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="92cdc-116">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="92cdc-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="92cdc-117">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="92cdc-117">Make the app's content localizable</span></span>

<span data-ttu-id="92cdc-118">Introduits dans ASP.NET Core, `IStringLocalizer` et `IStringLocalizer<T>` ont été conçus pour améliorer la productivité lors du développement d’applications localisées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="92cdc-119">`IStringLocalizer` utilise [ResourceManager](/dotnet/api/system.resources.resourcemanager) et [ResourceReader](/dotnet/api/system.resources.resourcereader) pour fournir les ressources propres à la culture au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="92cdc-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="92cdc-120">L’interface simple possède un indexeur et un `IEnumerable` pour retourner des chaînes localisées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="92cdc-121">`IStringLocalizer` n’exige pas de stocker les chaînes de langue par défaut dans un fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="92cdc-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="92cdc-122">Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement.</span><span class="sxs-lookup"><span data-stu-id="92cdc-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="92cdc-123">Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="92cdc-124">Dans le code ci-dessus, l’implémentation de `IStringLocalizer<T>` vient de l’[injection de dépendances](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="92cdc-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="92cdc-125">Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="92cdc-126">Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="92cdc-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="92cdc-127">Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="92cdc-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="92cdc-128">Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut.</span><span class="sxs-lookup"><span data-stu-id="92cdc-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="92cdc-129">Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="92cdc-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="92cdc-130">D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="92cdc-131">Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML.</span><span class="sxs-lookup"><span data-stu-id="92cdc-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="92cdc-132">`IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même.</span><span class="sxs-lookup"><span data-stu-id="92cdc-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="92cdc-133">Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.</span><span class="sxs-lookup"><span data-stu-id="92cdc-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="92cdc-134">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="92cdc-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="92cdc-135">Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :</span><span class="sxs-lookup"><span data-stu-id="92cdc-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="92cdc-136">Le code ci-dessus illustre chacune des deux méthodes de création.</span><span class="sxs-lookup"><span data-stu-id="92cdc-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="92cdc-137">Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="92cdc-138">Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="92cdc-139">Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="92cdc-140">Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="92cdc-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="92cdc-141">Localisation de l’affichage</span><span class="sxs-lookup"><span data-stu-id="92cdc-141">View localization</span></span>

<span data-ttu-id="92cdc-142">Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="92cdc-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="92cdc-143">La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue.</span><span class="sxs-lookup"><span data-stu-id="92cdc-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="92cdc-144">Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="92cdc-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="92cdc-145">L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="92cdc-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="92cdc-146">Il n’existe aucune option pour utiliser un fichier de ressources partagées globales.</span><span class="sxs-lookup"><span data-stu-id="92cdc-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="92cdc-147">`ViewLocalizer` implémente le localiseur en utilisant `IHtmlLocalizer`, si bien que Razor n’encode pas en HTML la chaîne localisée.</span><span class="sxs-lookup"><span data-stu-id="92cdc-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="92cdc-148">Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources.</span><span class="sxs-lookup"><span data-stu-id="92cdc-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="92cdc-149">Examinez le balisage Razor suivant :</span><span class="sxs-lookup"><span data-stu-id="92cdc-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="92cdc-150">Un fichier de ressources en français peut contenir ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="92cdc-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="92cdc-151">Clé</span><span class="sxs-lookup"><span data-stu-id="92cdc-151">Key</span></span> | <span data-ttu-id="92cdc-152">Valeur</span><span class="sxs-lookup"><span data-stu-id="92cdc-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="92cdc-153">L’affichage contient le balisage HTML provenant du fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="92cdc-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="92cdc-154">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="92cdc-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="92cdc-155">Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="92cdc-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="92cdc-156">Localisation de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="92cdc-156">DataAnnotations localization</span></span>

<span data-ttu-id="92cdc-157">Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="92cdc-158">En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :</span><span class="sxs-lookup"><span data-stu-id="92cdc-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="92cdc-159">*Ressources/ViewModels.Account.RegisterViewViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="92cdc-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="92cdc-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="92cdc-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="92cdc-161">Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés.</span><span class="sxs-lookup"><span data-stu-id="92cdc-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="92cdc-162">ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="92cdc-163">Utilisation d’une seule chaîne de ressource pour plusieurs classes</span><span class="sxs-lookup"><span data-stu-id="92cdc-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="92cdc-164">Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :</span><span class="sxs-lookup"><span data-stu-id="92cdc-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="92cdc-165">Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="92cdc-166">Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.</span><span class="sxs-lookup"><span data-stu-id="92cdc-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="92cdc-167">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="92cdc-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="92cdc-168">SupportedCultures et SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="92cdc-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="92cdc-169">ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="92cdc-170">L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises.</span><span class="sxs-lookup"><span data-stu-id="92cdc-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="92cdc-171">`SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes.</span><span class="sxs-lookup"><span data-stu-id="92cdc-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="92cdc-172">Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="92cdc-173">`SupportedUICultures` détermine les chaînes traduites (à partir de fichiers *.resx*) qui sont recherchées par [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="92cdc-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="92cdc-174">`ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="92cdc-175">Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="92cdc-176">ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="92cdc-177">Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="92cdc-178">Fichiers de ressources</span><span class="sxs-lookup"><span data-stu-id="92cdc-178">Resource files</span></span>

<span data-ttu-id="92cdc-179">Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code.</span><span class="sxs-lookup"><span data-stu-id="92cdc-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="92cdc-180">Les chaînes traduites pour la langue autre que celle par défaut sont des fichiers de ressources *.resx* isolés.</span><span class="sxs-lookup"><span data-stu-id="92cdc-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="92cdc-181">Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="92cdc-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="92cdc-182">« es » correspond au code de langue pour l’espagnol.</span><span class="sxs-lookup"><span data-stu-id="92cdc-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="92cdc-183">Pour créer ce fichier de ressources dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="92cdc-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="92cdc-184">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources.](localization/_static/newi.png)

2. <span data-ttu-id="92cdc-187">Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.</span><span class="sxs-lookup"><span data-stu-id="92cdc-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

3. <span data-ttu-id="92cdc-189">Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

    <span data-ttu-id="92cdc-191">Visual Studio présente le fichier *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="92cdc-193">Nommage du fichier de ressources</span><span class="sxs-lookup"><span data-stu-id="92cdc-193">Resource file naming</span></span>

<span data-ttu-id="92cdc-194">Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="92cdc-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="92cdc-195">Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="92cdc-196">Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="92cdc-197">Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="92cdc-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="92cdc-198">Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="92cdc-199">Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="92cdc-200">Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="92cdc-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="92cdc-201">Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="92cdc-202">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet.</span><span class="sxs-lookup"><span data-stu-id="92cdc-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="92cdc-203">Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="92cdc-204">Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="92cdc-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="92cdc-205">Nom de la ressource</span><span class="sxs-lookup"><span data-stu-id="92cdc-205">Resource name</span></span> | <span data-ttu-id="92cdc-206">Affectation de noms avec des points ou un chemin</span><span class="sxs-lookup"><span data-stu-id="92cdc-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="92cdc-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="92cdc-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="92cdc-208">Points</span><span class="sxs-lookup"><span data-stu-id="92cdc-208">Dot</span></span>  |
| <span data-ttu-id="92cdc-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="92cdc-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="92cdc-210">Path</span><span class="sxs-lookup"><span data-stu-id="92cdc-210">Path</span></span> |
|    |     |

<span data-ttu-id="92cdc-211">Les fichiers de ressources qui utilisent `@inject IViewLocalizer` dans les affichages Razor suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="92cdc-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="92cdc-212">Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin.</span><span class="sxs-lookup"><span data-stu-id="92cdc-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="92cdc-213">Les fichiers de ressources d’une vue Razor imitent le chemin de son fichier de vue associé.</span><span class="sxs-lookup"><span data-stu-id="92cdc-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="92cdc-214">Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :</span><span class="sxs-lookup"><span data-stu-id="92cdc-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="92cdc-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="92cdc-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="92cdc-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="92cdc-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="92cdc-217">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.</span><span class="sxs-lookup"><span data-stu-id="92cdc-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="92cdc-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="92cdc-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="92cdc-219">L’attribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fournit l’espace de noms racine d’un assembly quand il est différent du nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="92cdc-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="92cdc-220">Cela peut se produire lorsque le nom d’un projet n’est pas un identifiant .NET valide.</span><span class="sxs-lookup"><span data-stu-id="92cdc-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="92cdc-221">Par `my-project-name.csproj` exemple, utilisera `my_project_name` l’espace `my-project-name` de nom racine et le nom d’assemblage menant à cette erreur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="92cdc-222">Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :</span><span class="sxs-lookup"><span data-stu-id="92cdc-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="92cdc-223">La localisation ne fonctionne pas par défaut.</span><span class="sxs-lookup"><span data-stu-id="92cdc-223">Localization does not work by default.</span></span>
* <span data-ttu-id="92cdc-224">La localisation échoue en raison du mode de recherche des ressources dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="92cdc-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="92cdc-225">`RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant.</span><span class="sxs-lookup"><span data-stu-id="92cdc-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="92cdc-226">Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :</span><span class="sxs-lookup"><span data-stu-id="92cdc-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="92cdc-227">Le code précédent permet de résoudre correctement les fichiers resx.</span><span class="sxs-lookup"><span data-stu-id="92cdc-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="92cdc-228">Comportement de secours pour la culture</span><span class="sxs-lookup"><span data-stu-id="92cdc-228">Culture fallback behavior</span></span>

<span data-ttu-id="92cdc-229">Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="92cdc-230">Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="92cdc-231">Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente.</span><span class="sxs-lookup"><span data-stu-id="92cdc-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="92cdc-232">Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO.</span><span class="sxs-lookup"><span data-stu-id="92cdc-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="92cdc-233">Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="92cdc-234">Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.</span><span class="sxs-lookup"><span data-stu-id="92cdc-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="92cdc-235">Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="92cdc-236">Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :</span><span class="sxs-lookup"><span data-stu-id="92cdc-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="92cdc-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="92cdc-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="92cdc-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="92cdc-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="92cdc-239">*Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)</span><span class="sxs-lookup"><span data-stu-id="92cdc-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="92cdc-240">Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="92cdc-241">Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée.</span><span class="sxs-lookup"><span data-stu-id="92cdc-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="92cdc-242">Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="92cdc-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="92cdc-243">Générer des fichiers de ressources avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92cdc-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="92cdc-244">Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne.</span><span class="sxs-lookup"><span data-stu-id="92cdc-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="92cdc-245">Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92cdc-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="92cdc-246">Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture).</span><span class="sxs-lookup"><span data-stu-id="92cdc-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="92cdc-247">Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="92cdc-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="92cdc-248">Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.</span><span class="sxs-lookup"><span data-stu-id="92cdc-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="92cdc-249">Ajouter d’autres cultures</span><span class="sxs-lookup"><span data-stu-id="92cdc-249">Add other cultures</span></span>

<span data-ttu-id="92cdc-250">Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique.</span><span class="sxs-lookup"><span data-stu-id="92cdc-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="92cdc-251">Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et \*\* en gb\*\*).</span><span class="sxs-lookup"><span data-stu-id="92cdc-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="92cdc-252">Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).</span><span class="sxs-lookup"><span data-stu-id="92cdc-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="92cdc-253">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="92cdc-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="92cdc-254">Configurer la localisation</span><span class="sxs-lookup"><span data-stu-id="92cdc-254">Configure localization</span></span>

<span data-ttu-id="92cdc-255">La localisation est configurée dans la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="92cdc-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="92cdc-256">`AddLocalization` ajoute les services de localisation au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="92cdc-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="92cdc-257">Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="92cdc-258">`AddViewLocalization` ajoute la prise en charge des fichiers d’affichage localisés.</span><span class="sxs-lookup"><span data-stu-id="92cdc-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="92cdc-259">Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage.</span><span class="sxs-lookup"><span data-stu-id="92cdc-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="92cdc-260">Par exemple, « fr » dans le fichier *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="92cdc-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="92cdc-261">`AddDataAnnotationsLocalization` ajoute la prise en charge des messages de validation `DataAnnotations` localisés par le biais d’abstractions `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="92cdc-262">Intergiciel (middleware) de localisation</span><span class="sxs-lookup"><span data-stu-id="92cdc-262">Localization middleware</span></span>

<span data-ttu-id="92cdc-263">La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="92cdc-264">L’intergiciel de localisation est activé dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="92cdc-265">L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="92cdc-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="92cdc-266">`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="92cdc-267">Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé.</span><span class="sxs-lookup"><span data-stu-id="92cdc-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="92cdc-268">Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :</span><span class="sxs-lookup"><span data-stu-id="92cdc-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="92cdc-269">La liste par défaut va du plus spécifique au moins spécifique.</span><span class="sxs-lookup"><span data-stu-id="92cdc-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="92cdc-270">Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé.</span><span class="sxs-lookup"><span data-stu-id="92cdc-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="92cdc-271">Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="92cdc-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="92cdc-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="92cdc-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="92cdc-273">Certaines applications utilisent une chaîne de requête pour définir la [culture et la culture d’interface utilisateur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="92cdc-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="92cdc-274">Pour les applications qui utilisent l’approche du cookie ou de l’en-tête Accept-Language, l’ajout d’une chaîne de requête à l’URL s’avère utile pour déboguer et tester le code.</span><span class="sxs-lookup"><span data-stu-id="92cdc-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="92cdc-275">Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="92cdc-276">Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="92cdc-277">L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :</span><span class="sxs-lookup"><span data-stu-id="92cdc-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="92cdc-278">Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée.</span><span class="sxs-lookup"><span data-stu-id="92cdc-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="92cdc-279">Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="92cdc-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="92cdc-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="92cdc-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="92cdc-281">Les applications de production fournissent souvent un mécanisme permettant de définir la culture à l’aide du cookie de culture ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92cdc-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="92cdc-282">Utilisez la méthode `MakeCookieValue` pour créer un cookie.</span><span class="sxs-lookup"><span data-stu-id="92cdc-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="92cdc-283">Les `CookieRequestCultureProvider` `DefaultCookieName` retours du nom de cookie par défaut utilisé pour suivre les informations de culture préférées de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="92cdc-284">Le nom du cookie par défaut est `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="92cdc-285">Le format du cookie est `c=%LANGCODE%|uic=%LANGCODE%`, où `c` correspond à `Culture` et `uic` correspond à `UICulture`, par exemple :</span><span class="sxs-lookup"><span data-stu-id="92cdc-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="92cdc-286">Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="92cdc-287">En-tête HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="92cdc-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="92cdc-288">L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="92cdc-289">Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="92cdc-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="92cdc-290">L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="92cdc-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="92cdc-291">Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="92cdc-292">Définir l’en-tête HTTP Accept-Language dans IE</span><span class="sxs-lookup"><span data-stu-id="92cdc-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="92cdc-293">À partir de l’icône d’engrenage, appuyez sur **Options Internet**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="92cdc-294">Appuyez sur **Langues**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-294">Tap **Languages**.</span></span>

    ![Options Internet](localization/_static/lang.png)

3. <span data-ttu-id="92cdc-296">Appuyez sur **Définir les langues**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="92cdc-297">Appuyez sur **Ajouter une langue**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="92cdc-298">Ajoutez la langue.</span><span class="sxs-lookup"><span data-stu-id="92cdc-298">Add the language.</span></span>

6. <span data-ttu-id="92cdc-299">Tapez sur la langue, puis sur **Monter**.</span><span class="sxs-lookup"><span data-stu-id="92cdc-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="92cdc-300">L’en-tête content-langage HTTP</span><span class="sxs-lookup"><span data-stu-id="92cdc-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="92cdc-301">L’en-tête [de l’entité Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="92cdc-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="92cdc-302">Est utilisé pour décrire la langue (s) destinée au public.</span><span class="sxs-lookup"><span data-stu-id="92cdc-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="92cdc-303">Permet à un utilisateur de se différencier selon la langue préférée des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="92cdc-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="92cdc-304">Les en-têtes d’entité sont utilisés dans les demandes et réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="92cdc-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="92cdc-305">L’en-tête `Content-Language` peut être `ApplyCurrentCultureToResponseHeaders`ajouté en définissant la propriété .</span><span class="sxs-lookup"><span data-stu-id="92cdc-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="92cdc-306">Ajout `Content-Language` de l’en-tête:</span><span class="sxs-lookup"><span data-stu-id="92cdc-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="92cdc-307">Permet à la RequestLocalizationMiddleware de régler l’en-tête `Content-Language` avec le `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="92cdc-308">Élimine explicitement la nécessité de `Content-Language` définir explicitement l’en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="92cdc-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="92cdc-309">Utiliser un fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="92cdc-309">Use a custom provider</span></span>

<span data-ttu-id="92cdc-310">Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="92cdc-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="92cdc-311">Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92cdc-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="92cdc-312">Le code suivant illustre l’ajout d’un fournisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="92cdc-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="92cdc-313">Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="92cdc-314">Définir la culture par programmation</span><span class="sxs-lookup"><span data-stu-id="92cdc-314">Set the culture programmatically</span></span>

<span data-ttu-id="92cdc-315">Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`.</span><span class="sxs-lookup"><span data-stu-id="92cdc-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="92cdc-316">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :</span><span class="sxs-lookup"><span data-stu-id="92cdc-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="92cdc-317">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="92cdc-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="92cdc-318">La méthode `SetLanguage` définit le cookie de la culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="92cdc-319">Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet.</span><span class="sxs-lookup"><span data-stu-id="92cdc-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="92cdc-320">Le projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) comporte du code pour faire passer `RequestLocalizationOptions` à une ligne de code partiellement exécutée Razor par le biais du conteneur [Injection de dépendances](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="92cdc-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="92cdc-321">Données d’itinéraires de liaison de modèle et chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="92cdc-321">Model binding route data and query strings</span></span>

<span data-ttu-id="92cdc-322">Voir [Le comportement de la mondialisation des données d’itinéraire de liaison de modèle et](xref:mvc/models/model-binding#glob)des chaînes de requête.</span><span class="sxs-lookup"><span data-stu-id="92cdc-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="92cdc-323">Terminologie de la globalisation et de la localisation</span><span class="sxs-lookup"><span data-stu-id="92cdc-323">Globalization and localization terms</span></span>

<span data-ttu-id="92cdc-324">Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés.</span><span class="sxs-lookup"><span data-stu-id="92cdc-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="92cdc-325">Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents.</span><span class="sxs-lookup"><span data-stu-id="92cdc-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="92cdc-326">Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="92cdc-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="92cdc-327">L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.</span><span class="sxs-lookup"><span data-stu-id="92cdc-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="92cdc-328">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="92cdc-329">Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie).</span><span class="sxs-lookup"><span data-stu-id="92cdc-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="92cdc-330">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région.</span><span class="sxs-lookup"><span data-stu-id="92cdc-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="92cdc-331">Consultez [Nom de culture de la langue](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="92cdc-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="92cdc-332">L’internationalisation est souvent abrégée par « I18N ».</span><span class="sxs-lookup"><span data-stu-id="92cdc-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="92cdc-333">Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final.</span><span class="sxs-lookup"><span data-stu-id="92cdc-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="92cdc-334">Il en va de même pour la globalisation (G11N) et la localisation (L10N).</span><span class="sxs-lookup"><span data-stu-id="92cdc-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="92cdc-335">Termes :</span><span class="sxs-lookup"><span data-stu-id="92cdc-335">Terms:</span></span>

* <span data-ttu-id="92cdc-336">Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.</span><span class="sxs-lookup"><span data-stu-id="92cdc-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="92cdc-337">Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.</span><span class="sxs-lookup"><span data-stu-id="92cdc-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="92cdc-338">Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.</span><span class="sxs-lookup"><span data-stu-id="92cdc-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="92cdc-339">Culture : Correspond à une langue et éventuellement à une région.</span><span class="sxs-lookup"><span data-stu-id="92cdc-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="92cdc-340">Culture neutre : Culture dont la langue est spécifiée, mais pas la région.</span><span class="sxs-lookup"><span data-stu-id="92cdc-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="92cdc-341">(Exemples : « en », « es ».)</span><span class="sxs-lookup"><span data-stu-id="92cdc-341">(for example "en", "es")</span></span>
* <span data-ttu-id="92cdc-342">Culture spécifique : Culture dont la langue et la région sont spécifiées.</span><span class="sxs-lookup"><span data-stu-id="92cdc-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="92cdc-343">(Exemples : « en-US », « en-GB », « es-CL ».)</span><span class="sxs-lookup"><span data-stu-id="92cdc-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="92cdc-344">Culture parent : Culture neutre qui contient une culture spécifique.</span><span class="sxs-lookup"><span data-stu-id="92cdc-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="92cdc-345">(Exemple : « en » est la culture parent de « en-US » et « en-GB ».)</span><span class="sxs-lookup"><span data-stu-id="92cdc-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="92cdc-346">Paramètres régionaux : Synonyme de culture.</span><span class="sxs-lookup"><span data-stu-id="92cdc-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="92cdc-347">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="92cdc-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="92cdc-348">[Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.</span><span class="sxs-lookup"><span data-stu-id="92cdc-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="92cdc-349">Internationalisation et localisation d’applications .NET</span><span class="sxs-lookup"><span data-stu-id="92cdc-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="92cdc-350">Ressources dans les fichiers .resx</span><span class="sxs-lookup"><span data-stu-id="92cdc-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="92cdc-351">Kit de ressources pour application multilingue Microsoft</span><span class="sxs-lookup"><span data-stu-id="92cdc-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="92cdc-352">Localisation et classes génériques</span><span class="sxs-lookup"><span data-stu-id="92cdc-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
