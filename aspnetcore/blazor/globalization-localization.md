---
title: ASP.NET la Blazor mondialisation et la localisation de base
author: guardrex
description: Apprenez à rendre les composants Razor accessibles aux utilisateurs dans de multiples cultures et langues.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1b0db66b23c0caffc6b7c4e4af723c020609612a
ms.sourcegitcommit: d5d45d84fe488427d418de770000f7df44a08370
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81539659"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="ecea8-103">ASP.NET la Blazor mondialisation et la localisation de base</span><span class="sxs-lookup"><span data-stu-id="ecea8-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="ecea8-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ecea8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ecea8-105">Les composants razor peuvent être rendus accessibles aux utilisateurs dans de multiples cultures et langues.</span><span class="sxs-lookup"><span data-stu-id="ecea8-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="ecea8-106">Les scénarios suivants .NET de mondialisation et de localisation sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="ecea8-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="ecea8-107">. Système de ressources de NET</span><span class="sxs-lookup"><span data-stu-id="ecea8-107">.NET's resources system</span></span>
* <span data-ttu-id="ecea8-108">Formatage de nombre et de date spécifique à la culture</span><span class="sxs-lookup"><span data-stu-id="ecea8-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="ecea8-109">Un ensemble limité de scénarios de localisation de ASP.NET Core sont actuellement pris en charge :</span><span class="sxs-lookup"><span data-stu-id="ecea8-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="ecea8-110">`IStringLocalizer<>`*est* pris Blazor en charge dans les applications.</span><span class="sxs-lookup"><span data-stu-id="ecea8-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="ecea8-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`et la localisation des annotations de données sont ASP.NET scénarios Blazor Core MVC et non pris en **charge** dans les applications.</span><span class="sxs-lookup"><span data-stu-id="ecea8-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="ecea8-112">Pour plus d’informations, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ecea8-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="ecea8-113">Globalisation</span><span class="sxs-lookup"><span data-stu-id="ecea8-113">Globalization</span></span>

Blazor<span data-ttu-id="ecea8-114">la `@bind` fonctionnalité de l’utilisateur exécute des formats et analyse les valeurs d’affichage en fonction de la culture actuelle de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="ecea8-115">La culture actuelle peut être <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> accessible à partir de la propriété.</span><span class="sxs-lookup"><span data-stu-id="ecea8-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="ecea8-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) est utilisé pour les`<input type="{TYPE}" />`types de terrain suivants ( ):</span><span class="sxs-lookup"><span data-stu-id="ecea8-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="ecea8-117">Les types de champ précédents :</span><span class="sxs-lookup"><span data-stu-id="ecea8-117">The preceding field types:</span></span>

* <span data-ttu-id="ecea8-118">Sont affichés en utilisant leurs règles de formatage basées sur le navigateur appropriées.</span><span class="sxs-lookup"><span data-stu-id="ecea8-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="ecea8-119">Impossible de contenir du texte en forme libre.</span><span class="sxs-lookup"><span data-stu-id="ecea8-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="ecea8-120">Fournir des caractéristiques d’interaction utilisateur basées sur la mise en œuvre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="ecea8-121">Les types de champ suivants ont des exigences Blazor spécifiques de formatage et ne sont pas actuellement pris en charge par parce qu’ils ne sont pas pris en charge par tous les principaux navigateurs:</span><span class="sxs-lookup"><span data-stu-id="ecea8-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="ecea8-122">`@bind`prend `@bind:culture` en charge <xref:System.Globalization.CultureInfo?displayProperty=fullName> le paramètre pour fournir un pour analyser et formater une valeur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="ecea8-123">Spécifier une culture n’est `number` pas recommandé lors de l’utilisation des types et des `date` champs.</span><span class="sxs-lookup"><span data-stu-id="ecea8-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="ecea8-124">`date`et `number` ont un Blazor soutien intégré qui fournit la culture requise.</span><span class="sxs-lookup"><span data-stu-id="ecea8-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="ecea8-125">Localisation</span><span class="sxs-lookup"><span data-stu-id="ecea8-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="ecea8-126">WebAssembly (en)</span><span class="sxs-lookup"><span data-stu-id="ecea8-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="ecea8-127">Les applications WebAssembly configurent la culture en utilisant la [préférence linguistique](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)de l’utilisateur .</span><span class="sxs-lookup"><span data-stu-id="ecea8-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="ecea8-128">Configurer explicitement la `CultureInfo.DefaultThreadCurrentCulture` culture, définir et `CultureInfo.DefaultThreadCurrentUICulture` entrer `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="ecea8-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="ecea8-129">Par défaut, Blazorla configuration Blazor de liaison pour les applications WebAssembly supprime les informations d’internationalisation à l’exception des lieux explicitement demandés.</span><span class="sxs-lookup"><span data-stu-id="ecea8-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ecea8-130">Pour plus d’informations et de conseils sur <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>le contrôle du comportement du lien, voir .</span><span class="sxs-lookup"><span data-stu-id="ecea8-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="ecea8-131">Bien que Blazor la culture qui sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisager d’offrir un moyen pour les utilisateurs de spécifier leur local préféré.</span><span class="sxs-lookup"><span data-stu-id="ecea8-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="ecea8-132">Pour Blazor une application d’échantillon WebAssembly avec un cueilleur de culture, consultez [l’application LocSample.](https://github.com/pranavkm/LocSample)</span><span class="sxs-lookup"><span data-stu-id="ecea8-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="ecea8-133">Serveur</span><span class="sxs-lookup"><span data-stu-id="ecea8-133"> Server</span></span>

Blazor<span data-ttu-id="ecea8-134">Les applications serveur sont localisées à l’aide [de Localization Middleware](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="ecea8-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="ecea8-135">Le middleware sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources de l’application.</span><span class="sxs-lookup"><span data-stu-id="ecea8-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="ecea8-136">La culture peut être définie à l’aide d’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ecea8-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="ecea8-137">Cookies</span><span class="sxs-lookup"><span data-stu-id="ecea8-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="ecea8-138">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="ecea8-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="ecea8-139">Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ecea8-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="ecea8-140">Cookies</span><span class="sxs-lookup"><span data-stu-id="ecea8-140">Cookies</span></span>

<span data-ttu-id="ecea8-141">Un cookie de culture de localisation peut persister la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="ecea8-142">Le cookie est `OnGet` créé par la méthode de la page d’accueil de l’application *(Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="ecea8-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="ecea8-143">Le Middleware de localisation lit le cookie sur les demandes ultérieures de définir la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="ecea8-144">L’utilisation d’un cookie garantit que la connexion WebSocket peut propager correctement la culture.</span><span class="sxs-lookup"><span data-stu-id="ecea8-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="ecea8-145">Si les systèmes de localisation sont basés sur le chemin de l’URL ou la chaîne de requête, le régime pourrait ne pas être en mesure de travailler avec WebSockets, donc ne parviennent pas à persister la culture.</span><span class="sxs-lookup"><span data-stu-id="ecea8-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="ecea8-146">Par conséquent, l’utilisation d’un cookie de culture de localisation est l’approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="ecea8-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="ecea8-147">Toute technique peut être utilisée pour attribuer une culture si la culture est persistée dans un cookie de localisation.</span><span class="sxs-lookup"><span data-stu-id="ecea8-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="ecea8-148">Si l’application dispose déjà d’un système de localisation établi pour le côté serveur ASP.NET Core, continuer à utiliser l’infrastructure de localisation existante de l’application et définir le cookie de la culture de localisation dans le schéma de l’application.</span><span class="sxs-lookup"><span data-stu-id="ecea8-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="ecea8-149">L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lu par le Middleware de localisation.</span><span class="sxs-lookup"><span data-stu-id="ecea8-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="ecea8-150">Créez un fichier *Pages/_Host.cshtml.cs* avec le Blazor contenu suivant dans l’application Server :</span><span class="sxs-lookup"><span data-stu-id="ecea8-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="ecea8-151">La localisation est gérée par l’application dans la séquence suivante des événements :</span><span class="sxs-lookup"><span data-stu-id="ecea8-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="ecea8-152">Le navigateur envoie une demande initiale DE HTTP à l’application.</span><span class="sxs-lookup"><span data-stu-id="ecea8-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="ecea8-153">La culture est assignée par la Localisation Middleware.</span><span class="sxs-lookup"><span data-stu-id="ecea8-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="ecea8-154">La `OnGet` méthode dans *_Host.cshtml.cs* persiste la culture dans un cookie dans le cadre de la réponse.</span><span class="sxs-lookup"><span data-stu-id="ecea8-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="ecea8-155">Le navigateur ouvre une connexion WebSocket pour créer une session serveur interactive. Blazor</span><span class="sxs-lookup"><span data-stu-id="ecea8-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="ecea8-156">Le Middleware de localisation lit le cookie et attribue la culture.</span><span class="sxs-lookup"><span data-stu-id="ecea8-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="ecea8-157">La Blazor session Server commence par la bonne culture.</span><span class="sxs-lookup"><span data-stu-id="ecea8-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="ecea8-158">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="ecea8-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="ecea8-159">Pour fournir une interface utilisateur pour permettre à un utilisateur de choisir une culture, une *approche basée sur la redirection* est recommandée.</span><span class="sxs-lookup"><span data-stu-id="ecea8-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="ecea8-160">Le processus est similaire à ce qui se passe dans une application web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée.</span><span class="sxs-lookup"><span data-stu-id="ecea8-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="ecea8-161">L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine.</span><span class="sxs-lookup"><span data-stu-id="ecea8-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="ecea8-162">L’application persiste la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="ecea8-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="ecea8-163">Le contrôleur définit la culture sélectionnée de l’utilisateur en un cookie et redirige l’utilisateur vers l’URI d’origine.</span><span class="sxs-lookup"><span data-stu-id="ecea8-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="ecea8-164">Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :</span><span class="sxs-lookup"><span data-stu-id="ecea8-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="ecea8-165">Utilisez `LocalRedirect` le résultat d’action pour empêcher les attaques de redirection ouverte.</span><span class="sxs-lookup"><span data-stu-id="ecea8-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="ecea8-166">Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="ecea8-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="ecea8-167">Le composant suivant montre un exemple de la façon d’effectuer la redirection initiale lorsque l’utilisateur sélectionne une culture :</span><span class="sxs-lookup"><span data-stu-id="ecea8-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="ecea8-168">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ecea8-168">Additional resources</span></span>

* <xref:fundamentals/localization>
