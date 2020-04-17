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
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET la Blazor mondialisation et la localisation de base

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants razor peuvent être rendus accessibles aux utilisateurs dans de multiples cultures et langues. Les scénarios suivants .NET de mondialisation et de localisation sont disponibles :

* . Système de ressources de NET
* Formatage de nombre et de date spécifique à la culture

Un ensemble limité de scénarios de localisation de ASP.NET Core sont actuellement pris en charge :

* `IStringLocalizer<>`*est* pris Blazor en charge dans les applications.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`et la localisation des annotations de données sont ASP.NET scénarios Blazor Core MVC et non pris en **charge** dans les applications.

Pour plus d’informations, consultez <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisation

Blazorla `@bind` fonctionnalité de l’utilisateur exécute des formats et analyse les valeurs d’affichage en fonction de la culture actuelle de l’utilisateur.

La culture actuelle peut être <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> accessible à partir de la propriété.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) est utilisé pour les`<input type="{TYPE}" />`types de terrain suivants ( ):

* `date`
* `number`

Les types de champ précédents :

* Sont affichés en utilisant leurs règles de formatage basées sur le navigateur appropriées.
* Impossible de contenir du texte en forme libre.
* Fournir des caractéristiques d’interaction utilisateur basées sur la mise en œuvre du navigateur.

Les types de champ suivants ont des exigences Blazor spécifiques de formatage et ne sont pas actuellement pris en charge par parce qu’ils ne sont pas pris en charge par tous les principaux navigateurs:

* `datetime-local`
* `month`
* `week`

`@bind`prend `@bind:culture` en charge <xref:System.Globalization.CultureInfo?displayProperty=fullName> le paramètre pour fournir un pour analyser et formater une valeur. Spécifier une culture n’est `number` pas recommandé lors de l’utilisation des types et des `date` champs. `date`et `number` ont un Blazor soutien intégré qui fournit la culture requise.

## <a name="localization"></a>Localisation

### <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly (en)

BlazorLes applications WebAssembly configurent la culture en utilisant la [préférence linguistique](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)de l’utilisateur .

Configurer explicitement la `CultureInfo.DefaultThreadCurrentCulture` culture, définir et `CultureInfo.DefaultThreadCurrentUICulture` entrer `Program.Main`.

Par défaut, Blazorla configuration Blazor de liaison pour les applications WebAssembly supprime les informations d’internationalisation à l’exception des lieux explicitement demandés. Pour plus d’informations et de conseils sur <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>le contrôle du comportement du lien, voir .

Bien que Blazor la culture qui sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisager d’offrir un moyen pour les utilisateurs de spécifier leur local préféré. Pour Blazor une application d’échantillon WebAssembly avec un cueilleur de culture, consultez [l’application LocSample.](https://github.com/pranavkm/LocSample)

### <a name="opno-locblazor-server"></a>BlazorServeur

BlazorLes applications serveur sont localisées à l’aide [de Localization Middleware](xref:fundamentals/localization#localization-middleware). Le middleware sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources de l’application.

La culture peut être définie à l’aide d’une des approches suivantes :

* [Cookies](#cookies)
* [Fournir l’interface utilisateur pour choisir la culture](#provide-ui-to-choose-the-culture)

Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Un cookie de culture de localisation peut persister la culture de l’utilisateur. Le cookie est `OnGet` créé par la méthode de la page d’accueil de l’application *(Pages/Host.cshtml.cs*). Le Middleware de localisation lit le cookie sur les demandes ultérieures de définir la culture de l’utilisateur. 

L’utilisation d’un cookie garantit que la connexion WebSocket peut propager correctement la culture. Si les systèmes de localisation sont basés sur le chemin de l’URL ou la chaîne de requête, le régime pourrait ne pas être en mesure de travailler avec WebSockets, donc ne parviennent pas à persister la culture. Par conséquent, l’utilisation d’un cookie de culture de localisation est l’approche recommandée.

Toute technique peut être utilisée pour attribuer une culture si la culture est persistée dans un cookie de localisation. Si l’application dispose déjà d’un système de localisation établi pour le côté serveur ASP.NET Core, continuer à utiliser l’infrastructure de localisation existante de l’application et définir le cookie de la culture de localisation dans le schéma de l’application.

L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lu par le Middleware de localisation. Créez un fichier *Pages/_Host.cshtml.cs* avec le Blazor contenu suivant dans l’application Server :

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

La localisation est gérée par l’application dans la séquence suivante des événements :

1. Le navigateur envoie une demande initiale DE HTTP à l’application.
1. La culture est assignée par la Localisation Middleware.
1. La `OnGet` méthode dans *_Host.cshtml.cs* persiste la culture dans un cookie dans le cadre de la réponse.
1. Le navigateur ouvre une connexion WebSocket pour créer une session serveur interactive. Blazor
1. Le Middleware de localisation lit le cookie et attribue la culture.
1. La Blazor session Server commence par la bonne culture.

#### <a name="provide-ui-to-choose-the-culture"></a>Fournir l’interface utilisateur pour choisir la culture

Pour fournir une interface utilisateur pour permettre à un utilisateur de choisir une culture, une *approche basée sur la redirection* est recommandée. Le processus est similaire à ce qui se passe dans une application web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée. L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine. 

L’application persiste la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur. Le contrôleur définit la culture sélectionnée de l’utilisateur en un cookie et redirige l’utilisateur vers l’URI d’origine.

Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :

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
> Utilisez `LocalRedirect` le résultat d’action pour empêcher les attaques de redirection ouverte. Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.

Le composant suivant montre un exemple de la façon d’effectuer la redirection initiale lorsque l’utilisateur sélectionne une culture :

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

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/localization>
