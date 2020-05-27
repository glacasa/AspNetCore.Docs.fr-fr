---
<span data-ttu-id="ca0c5-101">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-102">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-103">'Identity'</span></span>
- <span data-ttu-id="ca0c5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-105">'Razor'</span></span>
- <span data-ttu-id="ca0c5-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="ca0c5-107">Globalisation et localisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca0c5-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ca0c5-108">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ca0c5-109">Un site Web multilingue permet au site d’atteindre un public plus vaste.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="ca0c5-110">ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="ca0c5-111">L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="ca0c5-112">La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="ca0c5-113">La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="ca0c5-114">La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="ca0c5-115">Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="ca0c5-116">La localisation d’une application implique les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-116">App localization involves the following:</span></span>

1. <span data-ttu-id="ca0c5-117">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="ca0c5-118">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="ca0c5-119">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="ca0c5-120">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca0c5-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="ca0c5-121">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-121">Make the app's content localizable</span></span>

<span data-ttu-id="ca0c5-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer’ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="ca0c5-123">Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="ca0c5-124">Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="ca0c5-125">Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="ca0c5-126">Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="ca0c5-127">Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="ca0c5-128">Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="ca0c5-129">Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="ca0c5-130">Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="ca0c5-131">D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="ca0c5-132">Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="ca0c5-133">`IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="ca0c5-134">Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="ca0c5-135">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-136">Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="ca0c5-137">Le code ci-dessus illustre chacune des deux méthodes de création.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="ca0c5-138">Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="ca0c5-139">Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="ca0c5-140">Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="ca0c5-141">Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="ca0c5-142">Localisation de l’affichage</span><span class="sxs-lookup"><span data-stu-id="ca0c5-142">View localization</span></span>

<span data-ttu-id="ca0c5-143">Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="ca0c5-144">La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="ca0c5-145">Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="ca0c5-146">L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="ca0c5-147">Il n’existe aucune option pour utiliser un fichier de ressources partagées globales.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="ca0c5-148">`ViewLocalizer`implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="ca0c5-149">Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="ca0c5-150">Examinez le Razor balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="ca0c5-151">Un fichier de ressources en français peut contenir ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="ca0c5-152">Clé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-152">Key</span></span> | <span data-ttu-id="ca0c5-153">Valeur</span><span class="sxs-lookup"><span data-stu-id="ca0c5-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="ca0c5-154">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-155">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-156">'Identity'</span></span>
- <span data-ttu-id="ca0c5-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-158">'Razor'</span></span>
- <span data-ttu-id="ca0c5-159">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-159">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="ca0c5-161">L’affichage contient le balisage HTML provenant du fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="ca0c5-162">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-163">Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="ca0c5-164">Localisation de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="ca0c5-164">DataAnnotations localization</span></span>

<span data-ttu-id="ca0c5-165">Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="ca0c5-166">En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="ca0c5-167">*Ressources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="ca0c5-169">Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="ca0c5-170">ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="ca0c5-171">Utilisation d’une seule chaîne de ressource pour plusieurs classes</span><span class="sxs-lookup"><span data-stu-id="ca0c5-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="ca0c5-172">Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="ca0c5-173">Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="ca0c5-174">Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="ca0c5-175">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="ca0c5-176">SupportedCultures et SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="ca0c5-177">ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="ca0c5-178">L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="ca0c5-179">`SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="ca0c5-180">Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="ca0c5-181">Le `SupportedUICultures` détermine les chaînes traduites (à partir des fichiers *. resx* ) qui sont recherchées par le [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="ca0c5-182">`ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="ca0c5-183">Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="ca0c5-184">ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="ca0c5-185">Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="ca0c5-186">Fichiers de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-186">Resource files</span></span>

<span data-ttu-id="ca0c5-187">Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="ca0c5-188">Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="ca0c5-189">Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="ca0c5-190">« es » correspond au code de langue pour l’espagnol.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="ca0c5-191">Pour créer ce fichier de ressources dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="ca0c5-192">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources.](localization/_static/newi.png)

2. <span data-ttu-id="ca0c5-195">Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

3. <span data-ttu-id="ca0c5-197">Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

    <span data-ttu-id="ca0c5-199">Visual Studio présente le fichier *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="ca0c5-201">Nommage du fichier de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-201">Resource file naming</span></span>

<span data-ttu-id="ca0c5-202">Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="ca0c5-203">Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="ca0c5-204">Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-205">Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="ca0c5-206">Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="ca0c5-207">Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-208">Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="ca0c5-209">Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-210">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="ca0c5-211">Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-212">Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="ca0c5-213">Nom de la ressource</span><span class="sxs-lookup"><span data-stu-id="ca0c5-213">Resource name</span></span> | <span data-ttu-id="ca0c5-214">Affectation de noms avec des points ou un chemin</span><span class="sxs-lookup"><span data-stu-id="ca0c5-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="ca0c5-215">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-216">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-217">'Identity'</span></span>
- <span data-ttu-id="ca0c5-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-219">'Razor'</span></span>
- <span data-ttu-id="ca0c5-220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-221">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-222">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-223">'Identity'</span></span>
- <span data-ttu-id="ca0c5-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-225">'Razor'</span></span>
- <span data-ttu-id="ca0c5-226">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-227">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-228">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-229">'Identity'</span></span>
- <span data-ttu-id="ca0c5-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-231">'Razor'</span></span>
- <span data-ttu-id="ca0c5-232">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-233">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-234">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-235">'Identity'</span></span>
- <span data-ttu-id="ca0c5-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-237">'Razor'</span></span>
- <span data-ttu-id="ca0c5-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-238">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-239">------   | titre de--- : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-240">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-241">'Identity'</span></span>
- <span data-ttu-id="ca0c5-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-243">'Razor'</span></span>
- <span data-ttu-id="ca0c5-244">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-245">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-246">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-247">'Identity'</span></span>
- <span data-ttu-id="ca0c5-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-249">'Razor'</span></span>
- <span data-ttu-id="ca0c5-250">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-251">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-252">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-253">'Identity'</span></span>
- <span data-ttu-id="ca0c5-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-255">'Razor'</span></span>
- <span data-ttu-id="ca0c5-256">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-257">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-258">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-259">'Identity'</span></span>
- <span data-ttu-id="ca0c5-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-261">'Razor'</span></span>
- <span data-ttu-id="ca0c5-262">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-262">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-263">------- | | Ressources/contrôleurs. HomeController. fr. resx | Point | | Ressources/contrôleurs/HomeController. fr. resx | Chemin | |    |     |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="ca0c5-264">Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="ca0c5-265">Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="ca0c5-266">les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="ca0c5-267">Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="ca0c5-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="ca0c5-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="ca0c5-270">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="ca0c5-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="ca0c5-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="ca0c5-272">L’attribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fournit l’espace de noms racine d’un assembly quand il est différent du nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="ca0c5-273">Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="ca0c5-274">Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="ca0c5-275">Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="ca0c5-276">La localisation ne fonctionne pas par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-276">Localization does not work by default.</span></span>
* <span data-ttu-id="ca0c5-277">La localisation échoue en raison du mode de recherche des ressources dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="ca0c5-278">`RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="ca0c5-279">Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="ca0c5-280">Le code précédent permet de résoudre correctement les fichiers resx.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="ca0c5-281">Comportement de secours pour la culture</span><span class="sxs-lookup"><span data-stu-id="ca0c5-281">Culture fallback behavior</span></span>

<span data-ttu-id="ca0c5-282">Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="ca0c5-283">Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="ca0c5-284">Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="ca0c5-285">Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="ca0c5-286">Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="ca0c5-287">Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="ca0c5-288">Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="ca0c5-289">Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="ca0c5-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="ca0c5-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-292">*Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="ca0c5-293">Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="ca0c5-294">Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="ca0c5-295">Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="ca0c5-296">Générer des fichiers de ressources avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca0c5-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="ca0c5-297">Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="ca0c5-298">Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="ca0c5-299">Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="ca0c5-300">Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="ca0c5-301">Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="ca0c5-302">Ajouter d’autres cultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-302">Add other cultures</span></span>

<span data-ttu-id="ca0c5-303">Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="ca0c5-304">Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et \*\* en gb\*\*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="ca0c5-305">Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="ca0c5-306">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="ca0c5-307">Configurer la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-307">Configure localization</span></span>

<span data-ttu-id="ca0c5-308">La localisation est configurée dans la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="ca0c5-309">`AddLocalization` ajoute les services de localisation au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="ca0c5-310">Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="ca0c5-311">`AddViewLocalization` ajoute la prise en charge des fichiers d’affichage localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="ca0c5-312">Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="ca0c5-313">Par exemple, « fr » dans le fichier *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="ca0c5-314">`AddDataAnnotationsLocalization` ajoute la prise en charge des messages de validation `DataAnnotations` localisés par le biais d’abstractions `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="ca0c5-315">Intergiciel (middleware) de localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-315">Localization middleware</span></span>

<span data-ttu-id="ca0c5-316">La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="ca0c5-317">L’intergiciel de localisation est activé dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="ca0c5-318">L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ca0c5-319">`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="ca0c5-320">Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="ca0c5-321">Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="ca0c5-322">La liste par défaut va du plus spécifique au moins spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="ca0c5-323">Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="ca0c5-324">Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="ca0c5-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-326">Certaines applications utilisent une chaîne de requête pour définir la [culture et la culture d’interface utilisateur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="ca0c5-327">Pour les applications qui utilisent l’approche du cookie ou de l’en-tête Accept-Language, l’ajout d’une chaîne de requête à l’URL s’avère utile pour déboguer et tester le code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="ca0c5-328">Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="ca0c5-329">Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="ca0c5-330">L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="ca0c5-331">Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="ca0c5-332">Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="ca0c5-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-334">Les applications de production fournissent souvent un mécanisme permettant de définir la culture à l’aide du cookie de culture ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="ca0c5-335">Utilisez la méthode `MakeCookieValue` pour créer un cookie.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="ca0c5-336">`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom de cookie par défaut utilisé pour suivre les informations de culture préférées de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="ca0c5-337">Le nom du cookie par défaut est `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="ca0c5-338">Le format du cookie est `c=%LANGCODE%|uic=%LANGCODE%`, où `c` correspond à `Culture` et `uic` correspond à `UICulture`, par exemple :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="ca0c5-339">Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="ca0c5-340">En-tête HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="ca0c5-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="ca0c5-341">L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="ca0c5-342">Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="ca0c5-343">L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="ca0c5-344">Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="ca0c5-345">Définir l’en-tête HTTP Accept-Language dans IE</span><span class="sxs-lookup"><span data-stu-id="ca0c5-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="ca0c5-346">À partir de l’icône d’engrenage, appuyez sur **Options Internet**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="ca0c5-347">Appuyez sur **Langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-347">Tap **Languages**.</span></span>

    ![Options Internet](localization/_static/lang.png)

3. <span data-ttu-id="ca0c5-349">Appuyez sur **Définir les langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="ca0c5-350">Appuyez sur **Ajouter une langue**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="ca0c5-351">Ajoutez la langue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-351">Add the language.</span></span>

6. <span data-ttu-id="ca0c5-352">Tapez sur la langue, puis sur **Monter**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="ca0c5-353">Utiliser un fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-353">Use a custom provider</span></span>

<span data-ttu-id="ca0c5-354">Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="ca0c5-355">Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="ca0c5-356">Le code suivant illustre l’ajout d’un fournisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="ca0c5-357">Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="ca0c5-358">Définir la culture par programmation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-358">Set the culture programmatically</span></span>

<span data-ttu-id="ca0c5-359">Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="ca0c5-360">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="ca0c5-361">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="ca0c5-362">La méthode `SetLanguage` définit le cookie de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="ca0c5-363">Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="ca0c5-364">Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="ca0c5-365">Données d’itinéraire de liaison de modèle et chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-365">Model binding route data and query strings</span></span>

<span data-ttu-id="ca0c5-366">Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="ca0c5-367">Terminologie de la globalisation et de la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-367">Globalization and localization terms</span></span>

<span data-ttu-id="ca0c5-368">Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="ca0c5-369">Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="ca0c5-370">Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="ca0c5-371">L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="ca0c5-372">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="ca0c5-373">Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="ca0c5-374">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="ca0c5-375">Consultez [Nom de culture de la langue](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="ca0c5-376">L’internationalisation est souvent abrégée par « I18N ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="ca0c5-377">Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="ca0c5-378">Il en va de même pour la globalisation (G11N) et la localisation (L10N).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="ca0c5-379">Termes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-379">Terms:</span></span>

* <span data-ttu-id="ca0c5-380">Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="ca0c5-381">Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="ca0c5-382">Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="ca0c5-383">Culture : Correspond à une langue et éventuellement à une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="ca0c5-384">Culture neutre : Culture dont la langue est spécifiée, mais pas la région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="ca0c5-385">(Exemples : « en », « es ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-385">(for example "en", "es")</span></span>
* <span data-ttu-id="ca0c5-386">Culture spécifique : Culture dont la langue et la région sont spécifiées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="ca0c5-387">(Exemples : « en-US », « en-GB », « es-CL ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="ca0c5-388">Culture parent : Culture neutre qui contient une culture spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="ca0c5-389">(Exemple : « en » est la culture parent de « en-US » et « en-GB ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="ca0c5-390">Paramètres régionaux : Synonyme de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="ca0c5-391">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ca0c5-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="ca0c5-392">[Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="ca0c5-393">Internationalisation et localisation d’applications .NET</span><span class="sxs-lookup"><span data-stu-id="ca0c5-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="ca0c5-394">Ressources dans les fichiers. resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="ca0c5-395">Kit de ressources pour application multilingue Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca0c5-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="ca0c5-396">Localisation et classes génériques</span><span class="sxs-lookup"><span data-stu-id="ca0c5-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca0c5-397">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ca0c5-398">Un site Web multilingue permet au site d’atteindre un public plus vaste.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="ca0c5-399">ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="ca0c5-400">L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="ca0c5-401">La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="ca0c5-402">La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="ca0c5-403">La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="ca0c5-404">Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="ca0c5-405">La localisation d’une application implique les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-405">App localization involves the following:</span></span>

1. <span data-ttu-id="ca0c5-406">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="ca0c5-407">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="ca0c5-408">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="ca0c5-409">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca0c5-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="ca0c5-410">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-410">Make the app's content localizable</span></span>

<span data-ttu-id="ca0c5-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer’ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="ca0c5-412">Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="ca0c5-413">Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="ca0c5-414">Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="ca0c5-415">Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="ca0c5-416">Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="ca0c5-417">Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="ca0c5-418">Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="ca0c5-419">Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="ca0c5-420">D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="ca0c5-421">Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="ca0c5-422">`IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="ca0c5-423">Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="ca0c5-424">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-425">Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="ca0c5-426">Le code ci-dessus illustre chacune des deux méthodes de création.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="ca0c5-427">Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="ca0c5-428">Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="ca0c5-429">Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="ca0c5-430">Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="ca0c5-431">Localisation de l’affichage</span><span class="sxs-lookup"><span data-stu-id="ca0c5-431">View localization</span></span>

<span data-ttu-id="ca0c5-432">Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="ca0c5-433">La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="ca0c5-434">Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="ca0c5-435">L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="ca0c5-436">Il n’existe aucune option pour utiliser un fichier de ressources partagées globales.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="ca0c5-437">`ViewLocalizer`implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="ca0c5-438">Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="ca0c5-439">Examinez le Razor balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="ca0c5-440">Un fichier de ressources en français peut contenir ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="ca0c5-441">Clé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-441">Key</span></span> | <span data-ttu-id="ca0c5-442">Valeur</span><span class="sxs-lookup"><span data-stu-id="ca0c5-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="ca0c5-443">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-444">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-445">'Identity'</span></span>
- <span data-ttu-id="ca0c5-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-447">'Razor'</span></span>
- <span data-ttu-id="ca0c5-448">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-448">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="ca0c5-450">L’affichage contient le balisage HTML provenant du fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="ca0c5-451">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-452">Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="ca0c5-453">Localisation de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="ca0c5-453">DataAnnotations localization</span></span>

<span data-ttu-id="ca0c5-454">Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="ca0c5-455">En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="ca0c5-456">*Ressources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="ca0c5-458">Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="ca0c5-459">ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="ca0c5-460">Utilisation d’une seule chaîne de ressource pour plusieurs classes</span><span class="sxs-lookup"><span data-stu-id="ca0c5-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="ca0c5-461">Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="ca0c5-462">Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="ca0c5-463">Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="ca0c5-464">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="ca0c5-465">SupportedCultures et SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="ca0c5-466">ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="ca0c5-467">L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="ca0c5-468">`SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="ca0c5-469">Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="ca0c5-470">Le `SupportedUICultures` détermine les chaînes traduites (à partir des fichiers *. resx* ) qui sont recherchées par le [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="ca0c5-471">`ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="ca0c5-472">Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="ca0c5-473">ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="ca0c5-474">Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="ca0c5-475">Fichiers de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-475">Resource files</span></span>

<span data-ttu-id="ca0c5-476">Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="ca0c5-477">Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="ca0c5-478">Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="ca0c5-479">« es » correspond au code de langue pour l’espagnol.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="ca0c5-480">Pour créer ce fichier de ressources dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="ca0c5-481">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources.](localization/_static/newi.png)

2. <span data-ttu-id="ca0c5-484">Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

3. <span data-ttu-id="ca0c5-486">Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

    <span data-ttu-id="ca0c5-488">Visual Studio présente le fichier *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="ca0c5-490">Nommage du fichier de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-490">Resource file naming</span></span>

<span data-ttu-id="ca0c5-491">Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="ca0c5-492">Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="ca0c5-493">Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-494">Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="ca0c5-495">Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="ca0c5-496">Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-497">Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="ca0c5-498">Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-499">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="ca0c5-500">Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-501">Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="ca0c5-502">Nom de la ressource</span><span class="sxs-lookup"><span data-stu-id="ca0c5-502">Resource name</span></span> | <span data-ttu-id="ca0c5-503">Affectation de noms avec des points ou un chemin</span><span class="sxs-lookup"><span data-stu-id="ca0c5-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="ca0c5-504">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-505">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-506">'Identity'</span></span>
- <span data-ttu-id="ca0c5-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-508">'Razor'</span></span>
- <span data-ttu-id="ca0c5-509">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-510">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-511">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-512">'Identity'</span></span>
- <span data-ttu-id="ca0c5-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-514">'Razor'</span></span>
- <span data-ttu-id="ca0c5-515">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-516">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-517">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-518">'Identity'</span></span>
- <span data-ttu-id="ca0c5-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-520">'Razor'</span></span>
- <span data-ttu-id="ca0c5-521">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-522">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-523">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-524">'Identity'</span></span>
- <span data-ttu-id="ca0c5-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-526">'Razor'</span></span>
- <span data-ttu-id="ca0c5-527">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-527">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-528">------   | titre de--- : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-529">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-530">'Identity'</span></span>
- <span data-ttu-id="ca0c5-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-532">'Razor'</span></span>
- <span data-ttu-id="ca0c5-533">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-534">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-535">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-536">'Identity'</span></span>
- <span data-ttu-id="ca0c5-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-538">'Razor'</span></span>
- <span data-ttu-id="ca0c5-539">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-540">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-541">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-542">'Identity'</span></span>
- <span data-ttu-id="ca0c5-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-544">'Razor'</span></span>
- <span data-ttu-id="ca0c5-545">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-546">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-547">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-548">'Identity'</span></span>
- <span data-ttu-id="ca0c5-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-550">'Razor'</span></span>
- <span data-ttu-id="ca0c5-551">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-551">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-552">------- | | Ressources/contrôleurs. HomeController. fr. resx | Point | | Ressources/contrôleurs/HomeController. fr. resx | Chemin | |    |     |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="ca0c5-553">Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="ca0c5-554">Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="ca0c5-555">les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="ca0c5-556">Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="ca0c5-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="ca0c5-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="ca0c5-559">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="ca0c5-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="ca0c5-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="ca0c5-561">L’attribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fournit l’espace de noms racine d’un assembly quand il est différent du nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="ca0c5-562">Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="ca0c5-563">Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="ca0c5-564">Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="ca0c5-565">La localisation ne fonctionne pas par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-565">Localization does not work by default.</span></span>
* <span data-ttu-id="ca0c5-566">La localisation échoue en raison du mode de recherche des ressources dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="ca0c5-567">`RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="ca0c5-568">Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="ca0c5-569">Le code précédent permet de résoudre correctement les fichiers resx.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="ca0c5-570">Comportement de secours pour la culture</span><span class="sxs-lookup"><span data-stu-id="ca0c5-570">Culture fallback behavior</span></span>

<span data-ttu-id="ca0c5-571">Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="ca0c5-572">Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="ca0c5-573">Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="ca0c5-574">Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="ca0c5-575">Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="ca0c5-576">Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="ca0c5-577">Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="ca0c5-578">Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="ca0c5-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="ca0c5-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-581">*Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="ca0c5-582">Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="ca0c5-583">Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="ca0c5-584">Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="ca0c5-585">Générer des fichiers de ressources avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca0c5-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="ca0c5-586">Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="ca0c5-587">Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="ca0c5-588">Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="ca0c5-589">Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="ca0c5-590">Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="ca0c5-591">Ajouter d’autres cultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-591">Add other cultures</span></span>

<span data-ttu-id="ca0c5-592">Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="ca0c5-593">Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et \*\* en gb\*\*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="ca0c5-594">Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="ca0c5-595">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="ca0c5-596">Configurer la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-596">Configure localization</span></span>

<span data-ttu-id="ca0c5-597">La localisation est configurée dans la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="ca0c5-598">`AddLocalization` ajoute les services de localisation au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="ca0c5-599">Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="ca0c5-600">`AddViewLocalization` ajoute la prise en charge des fichiers d’affichage localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="ca0c5-601">Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="ca0c5-602">Par exemple, « fr » dans le fichier *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="ca0c5-603">`AddDataAnnotationsLocalization` ajoute la prise en charge des messages de validation `DataAnnotations` localisés par le biais d’abstractions `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="ca0c5-604">Intergiciel (middleware) de localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-604">Localization middleware</span></span>

<span data-ttu-id="ca0c5-605">La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="ca0c5-606">L’intergiciel de localisation est activé dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="ca0c5-607">L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ca0c5-608">`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="ca0c5-609">Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="ca0c5-610">Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="ca0c5-611">La liste par défaut va du plus spécifique au moins spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="ca0c5-612">Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="ca0c5-613">Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="ca0c5-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-615">Certaines applications utilisent une chaîne de requête pour définir la [culture et la culture d’interface utilisateur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="ca0c5-616">Pour les applications qui utilisent l’approche du cookie ou de l’en-tête Accept-Language, l’ajout d’une chaîne de requête à l’URL s’avère utile pour déboguer et tester le code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="ca0c5-617">Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="ca0c5-618">Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="ca0c5-619">L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="ca0c5-620">Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="ca0c5-621">Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="ca0c5-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-623">Les applications de production fournissent souvent un mécanisme permettant de définir la culture à l’aide du cookie de culture ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="ca0c5-624">Utilisez la méthode `MakeCookieValue` pour créer un cookie.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="ca0c5-625">`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom de cookie par défaut utilisé pour suivre les informations de culture préférées de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="ca0c5-626">Le nom du cookie par défaut est `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="ca0c5-627">Le format du cookie est `c=%LANGCODE%|uic=%LANGCODE%`, où `c` correspond à `Culture` et `uic` correspond à `UICulture`, par exemple :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="ca0c5-628">Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="ca0c5-629">En-tête HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="ca0c5-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="ca0c5-630">L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="ca0c5-631">Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="ca0c5-632">L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="ca0c5-633">Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="ca0c5-634">Définir l’en-tête HTTP Accept-Language dans IE</span><span class="sxs-lookup"><span data-stu-id="ca0c5-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="ca0c5-635">À partir de l’icône d’engrenage, appuyez sur **Options Internet**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="ca0c5-636">Appuyez sur **Langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-636">Tap **Languages**.</span></span>

    ![Options Internet](localization/_static/lang.png)

3. <span data-ttu-id="ca0c5-638">Appuyez sur **Définir les langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="ca0c5-639">Appuyez sur **Ajouter une langue**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="ca0c5-640">Ajoutez la langue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-640">Add the language.</span></span>

6. <span data-ttu-id="ca0c5-641">Tapez sur la langue, puis sur **Monter**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="ca0c5-642">Utiliser un fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-642">Use a custom provider</span></span>

<span data-ttu-id="ca0c5-643">Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="ca0c5-644">Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="ca0c5-645">Le code suivant illustre l’ajout d’un fournisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="ca0c5-646">Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="ca0c5-647">Définir la culture par programmation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-647">Set the culture programmatically</span></span>

<span data-ttu-id="ca0c5-648">Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="ca0c5-649">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="ca0c5-650">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="ca0c5-651">La méthode `SetLanguage` définit le cookie de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="ca0c5-652">Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="ca0c5-653">Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="ca0c5-654">Données d’itinéraire de liaison de modèle et chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-654">Model binding route data and query strings</span></span>

<span data-ttu-id="ca0c5-655">Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="ca0c5-656">Terminologie de la globalisation et de la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-656">Globalization and localization terms</span></span>

<span data-ttu-id="ca0c5-657">Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="ca0c5-658">Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="ca0c5-659">Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="ca0c5-660">L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="ca0c5-661">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="ca0c5-662">Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="ca0c5-663">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="ca0c5-664">Consultez [Nom de culture de la langue](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="ca0c5-665">L’internationalisation est souvent abrégée par « I18N ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="ca0c5-666">Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="ca0c5-667">Il en va de même pour la globalisation (G11N) et la localisation (L10N).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="ca0c5-668">Termes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-668">Terms:</span></span>

* <span data-ttu-id="ca0c5-669">Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="ca0c5-670">Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="ca0c5-671">Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="ca0c5-672">Culture : Correspond à une langue et éventuellement à une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="ca0c5-673">Culture neutre : Culture dont la langue est spécifiée, mais pas la région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="ca0c5-674">(Exemples : « en », « es ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-674">(for example "en", "es")</span></span>
* <span data-ttu-id="ca0c5-675">Culture spécifique : Culture dont la langue et la région sont spécifiées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="ca0c5-676">(Exemples : « en-US », « en-GB », « es-CL ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="ca0c5-677">Culture parent : Culture neutre qui contient une culture spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="ca0c5-678">(Exemple : « en » est la culture parent de « en-US » et « en-GB ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="ca0c5-679">Paramètres régionaux : Synonyme de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="ca0c5-680">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ca0c5-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="ca0c5-681">[Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="ca0c5-682">Internationalisation et localisation d’applications .NET</span><span class="sxs-lookup"><span data-stu-id="ca0c5-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="ca0c5-683">Ressources dans les fichiers. resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="ca0c5-684">Kit de ressources pour application multilingue Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca0c5-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="ca0c5-685">Localisation et classes génériques</span><span class="sxs-lookup"><span data-stu-id="ca0c5-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="ca0c5-686">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ca0c5-687">Un site Web multilingue permet au site d’atteindre un public plus vaste.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="ca0c5-688">ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="ca0c5-689">L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="ca0c5-690">La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="ca0c5-691">La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="ca0c5-692">La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="ca0c5-693">Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="ca0c5-694">La localisation d’une application implique les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-694">App localization involves the following:</span></span>

1. <span data-ttu-id="ca0c5-695">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="ca0c5-696">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="ca0c5-697">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="ca0c5-698">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca0c5-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="ca0c5-699">Rendre le contenu de l’application localisable</span><span class="sxs-lookup"><span data-stu-id="ca0c5-699">Make the app's content localizable</span></span>

<span data-ttu-id="ca0c5-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer’ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="ca0c5-701">Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="ca0c5-702">Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="ca0c5-703">Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="ca0c5-704">Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="ca0c5-705">Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="ca0c5-706">Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="ca0c5-707">Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="ca0c5-708">Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="ca0c5-709">D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="ca0c5-710">Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="ca0c5-711">`IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="ca0c5-712">Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="ca0c5-713">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-714">Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="ca0c5-715">Le code ci-dessus illustre chacune des deux méthodes de création.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="ca0c5-716">Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="ca0c5-717">Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="ca0c5-718">Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="ca0c5-719">Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="ca0c5-720">Localisation de l’affichage</span><span class="sxs-lookup"><span data-stu-id="ca0c5-720">View localization</span></span>

<span data-ttu-id="ca0c5-721">Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="ca0c5-722">La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="ca0c5-723">Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="ca0c5-724">L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="ca0c5-725">Il n’existe aucune option pour utiliser un fichier de ressources partagées globales.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="ca0c5-726">`ViewLocalizer`implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="ca0c5-727">Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="ca0c5-728">Examinez le Razor balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="ca0c5-729">Un fichier de ressources en français peut contenir ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="ca0c5-730">Clé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-730">Key</span></span> | <span data-ttu-id="ca0c5-731">Valeur</span><span class="sxs-lookup"><span data-stu-id="ca0c5-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="ca0c5-732">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-733">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-734">'Identity'</span></span>
- <span data-ttu-id="ca0c5-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-736">'Razor'</span></span>
- <span data-ttu-id="ca0c5-737">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-737">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="ca0c5-739">L’affichage contient le balisage HTML provenant du fichier de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="ca0c5-740">**Remarque :** Vous avez généralement besoin de localiser uniquement le texte et pas le code HTML.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ca0c5-741">Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="ca0c5-742">Localisation de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="ca0c5-742">DataAnnotations localization</span></span>

<span data-ttu-id="ca0c5-743">Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="ca0c5-744">En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="ca0c5-745">*Ressources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="ca0c5-747">Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="ca0c5-748">ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="ca0c5-749">Utilisation d’une seule chaîne de ressource pour plusieurs classes</span><span class="sxs-lookup"><span data-stu-id="ca0c5-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="ca0c5-750">Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="ca0c5-751">Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="ca0c5-752">Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="ca0c5-753">Fournir des ressources localisées aux langues et cultures prises en charge</span><span class="sxs-lookup"><span data-stu-id="ca0c5-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="ca0c5-754">SupportedCultures et SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="ca0c5-755">ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="ca0c5-756">L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="ca0c5-757">`SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="ca0c5-758">Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="ca0c5-759">Le `SupportedUICultures` détermine les chaînes traduites (à partir des fichiers *. resx* ) qui sont recherchées par le [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="ca0c5-760">`ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="ca0c5-761">Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="ca0c5-762">ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="ca0c5-763">Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="ca0c5-764">Fichiers de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-764">Resource files</span></span>

<span data-ttu-id="ca0c5-765">Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="ca0c5-766">Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="ca0c5-767">Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="ca0c5-768">« es » correspond au code de langue pour l’espagnol.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="ca0c5-769">Pour créer ce fichier de ressources dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="ca0c5-770">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources.](localization/_static/newi.png)

2. <span data-ttu-id="ca0c5-773">Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

3. <span data-ttu-id="ca0c5-775">Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

    <span data-ttu-id="ca0c5-777">Visual Studio présente le fichier *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="ca0c5-779">Nommage du fichier de ressources</span><span class="sxs-lookup"><span data-stu-id="ca0c5-779">Resource file naming</span></span>

<span data-ttu-id="ca0c5-780">Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="ca0c5-781">Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="ca0c5-782">Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-783">Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="ca0c5-784">Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="ca0c5-785">Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-786">Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="ca0c5-787">Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-788">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="ca0c5-789">Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ca0c5-790">Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="ca0c5-791">Nom de la ressource</span><span class="sxs-lookup"><span data-stu-id="ca0c5-791">Resource name</span></span> | <span data-ttu-id="ca0c5-792">Affectation de noms avec des points ou un chemin</span><span class="sxs-lookup"><span data-stu-id="ca0c5-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="ca0c5-793">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-794">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-795">'Identity'</span></span>
- <span data-ttu-id="ca0c5-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-797">'Razor'</span></span>
- <span data-ttu-id="ca0c5-798">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-799">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-800">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-801">'Identity'</span></span>
- <span data-ttu-id="ca0c5-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-803">'Razor'</span></span>
- <span data-ttu-id="ca0c5-804">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-805">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-806">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-807">'Identity'</span></span>
- <span data-ttu-id="ca0c5-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-809">'Razor'</span></span>
- <span data-ttu-id="ca0c5-810">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-811">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-812">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-813">'Identity'</span></span>
- <span data-ttu-id="ca0c5-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-815">'Razor'</span></span>
- <span data-ttu-id="ca0c5-816">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-816">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-817">------   | titre de--- : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-818">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-819">'Identity'</span></span>
- <span data-ttu-id="ca0c5-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-821">'Razor'</span></span>
- <span data-ttu-id="ca0c5-822">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-823">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-824">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-825">'Identity'</span></span>
- <span data-ttu-id="ca0c5-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-827">'Razor'</span></span>
- <span data-ttu-id="ca0c5-828">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-829">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-830">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-831">'Identity'</span></span>
- <span data-ttu-id="ca0c5-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-833">'Razor'</span></span>
- <span data-ttu-id="ca0c5-834">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca0c5-835">titre : Auteur : Description : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca0c5-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-836">'Blazor'</span></span>
- <span data-ttu-id="ca0c5-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-837">'Identity'</span></span>
- <span data-ttu-id="ca0c5-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca0c5-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca0c5-839">'Razor'</span></span>
- <span data-ttu-id="ca0c5-840">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-840">'SignalR' uid:</span></span> 

<span data-ttu-id="ca0c5-841">------- | | Ressources/contrôleurs. HomeController. fr. resx | Point | | Ressources/contrôleurs/HomeController. fr. resx | Chemin | |    |     |</span><span class="sxs-lookup"><span data-stu-id="ca0c5-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="ca0c5-842">Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="ca0c5-843">Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="ca0c5-844">les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="ca0c5-845">Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="ca0c5-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="ca0c5-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="ca0c5-848">Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="ca0c5-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="ca0c5-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="ca0c5-850">L’attribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fournit l’espace de noms racine d’un assembly quand il est différent du nom de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="ca0c5-851">Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="ca0c5-852">Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="ca0c5-853">Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="ca0c5-854">La localisation ne fonctionne pas par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-854">Localization does not work by default.</span></span>
* <span data-ttu-id="ca0c5-855">La localisation échoue en raison du mode de recherche des ressources dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="ca0c5-856">`RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="ca0c5-857">Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="ca0c5-858">Le code précédent permet de résoudre correctement les fichiers resx.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="ca0c5-859">Comportement de secours pour la culture</span><span class="sxs-lookup"><span data-stu-id="ca0c5-859">Culture fallback behavior</span></span>

<span data-ttu-id="ca0c5-860">Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="ca0c5-861">Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="ca0c5-862">Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="ca0c5-863">Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="ca0c5-864">Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="ca0c5-865">Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="ca0c5-866">Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="ca0c5-867">Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="ca0c5-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="ca0c5-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ca0c5-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="ca0c5-870">*Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="ca0c5-871">Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="ca0c5-872">Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="ca0c5-873">Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="ca0c5-874">Générer des fichiers de ressources avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca0c5-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="ca0c5-875">Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="ca0c5-876">Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="ca0c5-877">Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="ca0c5-878">Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="ca0c5-879">Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="ca0c5-880">Ajouter d’autres cultures</span><span class="sxs-lookup"><span data-stu-id="ca0c5-880">Add other cultures</span></span>

<span data-ttu-id="ca0c5-881">Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="ca0c5-882">Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et \*\* en gb\*\*).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="ca0c5-883">Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="ca0c5-884">Implémenter une stratégie de sélection de la langue/culture pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="ca0c5-885">Configurer la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-885">Configure localization</span></span>

<span data-ttu-id="ca0c5-886">La localisation est configurée dans la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="ca0c5-887">`AddLocalization` ajoute les services de localisation au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="ca0c5-888">Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="ca0c5-889">`AddViewLocalization` ajoute la prise en charge des fichiers d’affichage localisés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="ca0c5-890">Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="ca0c5-891">Par exemple, « fr » dans le fichier *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="ca0c5-892">`AddDataAnnotationsLocalization` ajoute la prise en charge des messages de validation `DataAnnotations` localisés par le biais d’abstractions `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="ca0c5-893">Intergiciel (middleware) de localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-893">Localization middleware</span></span>

<span data-ttu-id="ca0c5-894">La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="ca0c5-895">L’intergiciel de localisation est activé dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="ca0c5-896">L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ca0c5-897">`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="ca0c5-898">Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="ca0c5-899">Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="ca0c5-900">La liste par défaut va du plus spécifique au moins spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="ca0c5-901">Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="ca0c5-902">Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="ca0c5-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-904">Certaines applications utilisent une chaîne de requête pour définir la [culture et la culture d’interface utilisateur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="ca0c5-905">Pour les applications qui utilisent l’approche du cookie ou de l’en-tête Accept-Language, l’ajout d’une chaîne de requête à l’URL s’avère utile pour déboguer et tester le code.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="ca0c5-906">Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="ca0c5-907">Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="ca0c5-908">L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="ca0c5-909">Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="ca0c5-910">Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="ca0c5-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ca0c5-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="ca0c5-912">Les applications de production fournissent souvent un mécanisme permettant de définir la culture à l’aide du cookie de culture ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="ca0c5-913">Utilisez la méthode `MakeCookieValue` pour créer un cookie.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="ca0c5-914">`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom de cookie par défaut utilisé pour suivre les informations de culture préférées de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="ca0c5-915">Le nom du cookie par défaut est `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="ca0c5-916">Le format du cookie est `c=%LANGCODE%|uic=%LANGCODE%`, où `c` correspond à `Culture` et `uic` correspond à `UICulture`, par exemple :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="ca0c5-917">Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="ca0c5-918">En-tête HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="ca0c5-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="ca0c5-919">L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="ca0c5-920">Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="ca0c5-921">L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="ca0c5-922">Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="ca0c5-923">Définir l’en-tête HTTP Accept-Language dans IE</span><span class="sxs-lookup"><span data-stu-id="ca0c5-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="ca0c5-924">À partir de l’icône d’engrenage, appuyez sur **Options Internet**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="ca0c5-925">Appuyez sur **Langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-925">Tap **Languages**.</span></span>

    ![Options Internet](localization/_static/lang.png)

3. <span data-ttu-id="ca0c5-927">Appuyez sur **Définir les langues**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="ca0c5-928">Appuyez sur **Ajouter une langue**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="ca0c5-929">Ajoutez la langue.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-929">Add the language.</span></span>

6. <span data-ttu-id="ca0c5-930">Tapez sur la langue, puis sur **Monter**.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="ca0c5-931">En-tête HTTP Content-Language</span><span class="sxs-lookup"><span data-stu-id="ca0c5-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="ca0c5-932">En-tête d’entité [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="ca0c5-933">Est utilisé pour décrire la ou les langues destinées au public.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="ca0c5-934">Permet à un utilisateur de faire la distinction en fonction de la langue préférée de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="ca0c5-935">Les en-têtes d’entité sont utilisés dans les requêtes et les réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="ca0c5-936">Vous `Content-Language` pouvez ajouter l’en-tête en définissant la propriété `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="ca0c5-937">Ajout de l' `Content-Language` en-tête :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="ca0c5-938">Permet à RequestLocalizationMiddleware de définir l' `Content-Language` en-tête avec `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="ca0c5-939">Élimine la nécessité de définir explicitement l’en-tête de réponse `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="ca0c5-940">Utiliser un fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="ca0c5-940">Use a custom provider</span></span>

<span data-ttu-id="ca0c5-941">Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="ca0c5-942">Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="ca0c5-943">Le code suivant illustre l’ajout d’un fournisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="ca0c5-944">Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="ca0c5-945">Définir la culture par programmation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-945">Set the culture programmatically</span></span>

<span data-ttu-id="ca0c5-946">Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="ca0c5-947">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="ca0c5-948">Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="ca0c5-949">La méthode `SetLanguage` définit le cookie de la culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="ca0c5-950">Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="ca0c5-951">Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="ca0c5-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="ca0c5-952">Données d’itinéraire de liaison de modèle et chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="ca0c5-952">Model binding route data and query strings</span></span>

<span data-ttu-id="ca0c5-953">Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="ca0c5-954">Terminologie de la globalisation et de la localisation</span><span class="sxs-lookup"><span data-stu-id="ca0c5-954">Globalization and localization terms</span></span>

<span data-ttu-id="ca0c5-955">Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="ca0c5-956">Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="ca0c5-957">Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="ca0c5-958">L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="ca0c5-959">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="ca0c5-960">Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="ca0c5-961">Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="ca0c5-962">Consultez [Nom de culture de la langue](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="ca0c5-963">L’internationalisation est souvent abrégée par « I18N ».</span><span class="sxs-lookup"><span data-stu-id="ca0c5-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="ca0c5-964">Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="ca0c5-965">Il en va de même pour la globalisation (G11N) et la localisation (L10N).</span><span class="sxs-lookup"><span data-stu-id="ca0c5-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="ca0c5-966">Termes :</span><span class="sxs-lookup"><span data-stu-id="ca0c5-966">Terms:</span></span>

* <span data-ttu-id="ca0c5-967">Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="ca0c5-968">Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="ca0c5-969">Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="ca0c5-970">Culture : Correspond à une langue et éventuellement à une région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="ca0c5-971">Culture neutre : Culture dont la langue est spécifiée, mais pas la région.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="ca0c5-972">(Exemples : « en », « es ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-972">(for example "en", "es")</span></span>
* <span data-ttu-id="ca0c5-973">Culture spécifique : Culture dont la langue et la région sont spécifiées.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="ca0c5-974">(Exemples : « en-US », « en-GB », « es-CL ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="ca0c5-975">Culture parent : Culture neutre qui contient une culture spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="ca0c5-976">(Exemple : « en » est la culture parent de « en-US » et « en-GB ».)</span><span class="sxs-lookup"><span data-stu-id="ca0c5-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="ca0c5-977">Paramètres régionaux : Synonyme de culture.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="ca0c5-978">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ca0c5-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="ca0c5-979">[Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.</span><span class="sxs-lookup"><span data-stu-id="ca0c5-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="ca0c5-980">Internationalisation et localisation d’applications .NET</span><span class="sxs-lookup"><span data-stu-id="ca0c5-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="ca0c5-981">Ressources dans les fichiers. resx</span><span class="sxs-lookup"><span data-stu-id="ca0c5-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="ca0c5-982">Kit de ressources pour application multilingue Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca0c5-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="ca0c5-983">Localisation et classes génériques</span><span class="sxs-lookup"><span data-stu-id="ca0c5-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
