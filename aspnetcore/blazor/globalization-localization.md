---
title: Globalisation et localisation ASP.NET Core Blazor
author: guardrex
description: Découvrez comment rendre Razor des composants accessibles aux utilisateurs dans plusieurs cultures et langages.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772489"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>Globalisation et localisation ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Razorles composants peuvent être rendus accessibles aux utilisateurs dans plusieurs cultures et langues. Les scénarios de globalisation et de localisation .NET suivants sont disponibles :

* . Système de ressources du réseau
* Mise en forme des nombres et des dates spécifiques à la culture

Un ensemble limité de scénarios de localisation de ASP.NET Core est actuellement pris en charge :

* `IStringLocalizer<>`*est pris en charge* dans Blazor les applications.
* `IHtmlLocalizer<>`la `IViewLocalizer<>`localisation des annotations de données, et est ASP.net Core les scénarios MVC Blazor et **non pris en charge** dans les applications.

Pour plus d’informations, consultez <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisation

Blazorla `@bind` fonctionnalité de effectue des mises en forme et analyse les valeurs pour l’affichage en fonction de la culture actuelle de l’utilisateur.

La culture actuelle est accessible à partir de <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> la propriété.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) est utilisé pour les types de champ suivants`<input type="{TYPE}" />`() :

* `date`
* `number`

Les types de champ précédents :

* Sont affichés à l’aide des règles de mise en forme appropriées basées sur le navigateur.
* Ne peut pas contenir de texte de forme libre.
* Fournir des caractéristiques d’interaction de l’utilisateur en fonction de l’implémentation du navigateur.

Les types de champs suivants ont des exigences de mise en forme spécifiques et Blazor ne sont pas actuellement pris en charge par, car ils ne sont pas pris en charge par tous les principaux navigateurs :

* `datetime-local`
* `month`
* `week`

`@bind`prend en `@bind:culture` charge le paramètre pour <xref:System.Globalization.CultureInfo?displayProperty=fullName> fournir un pour l’analyse et la mise en forme d’une valeur. La `date` spécification d’une culture n’est pas recommandée `number` lors de l’utilisation des types de champ et. `date`et `number` disposent d' Blazor une prise en charge intégrée qui fournit la culture requise.

## <a name="localization"></a>Localisation

### <a name="blazor-webassembly"></a>BlazorWebassembly

BlazorLes applications webassembly définissent la culture à l’aide de la [préférence de langue](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)de l’utilisateur.

Pour configurer explicitement la culture, définissez `CultureInfo.DefaultThreadCurrentCulture` et `CultureInfo.DefaultThreadCurrentUICulture` dans `Program.Main`.

Par défaut, Blazorla configuration de l’éditeur Blazor de liens pour les applications webassembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement. Pour plus d’informations et de conseils sur le contrôle du comportement de l' <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>éditeur de liens, consultez.

Alors que la culture Blazor qui sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisagez d’offrir aux utilisateurs un moyen de spécifier leurs paramètres régionaux préférés. Pour obtenir Blazor un exemple d’application webassembly avec un sélecteur de culture, consultez l’exemple d’application de localisation [LocSample](https://github.com/pranavkm/LocSample) .

### <a name="blazor-server"></a>BlazorServeurs

BlazorLes applications serveur sont localisées à l’aide de l' [intergiciel (middleware](xref:fundamentals/localization#localization-middleware)) de localisation. L’intergiciel sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources à partir de l’application.

La culture peut être définie à l’aide de l’une des approches suivantes :

* [Cookies](#cookies)
* [Fournir l’interface utilisateur pour choisir la culture](#provide-ui-to-choose-the-culture)

Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Un cookie de culture de localisation peut conserver la culture de l’utilisateur. Le cookie est créé par la `OnGet` méthode de la page hôte de l’application (*pages/Host. cshtml. cs*). L’intergiciel (middleware) de localisation lit le cookie sur les demandes suivantes pour définir la culture de l’utilisateur. 

L’utilisation d’un cookie garantit que la connexion WebSocket peut propager correctement la culture. Si les schémas de localisation sont basés sur le chemin d’URL ou la chaîne de requête, il est possible que le schéma ne soit pas en mesure de fonctionner avec les WebSockets, et donc de ne pas conserver la culture. Par conséquent, l’utilisation d’un cookie de culture de localisation est l’approche recommandée.

Toute technique peut être utilisée pour assigner une culture si la culture est rendue persistante dans un cookie de localisation. Si l’application a déjà un schéma de localisation établi pour les ASP.NET Core côté serveur, continuez à utiliser l’infrastructure de localisation existante de l’application et à définir le cookie de la culture de localisation dans le schéma de l’application.

L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lu par l’intergiciel (middleware) de localisation. Créez un fichier *pages/_Host. cshtml. cs* avec le contenu suivant dans l' Blazor application serveur :

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

La localisation est gérée par l’application dans la séquence d’événements suivante :

1. Le navigateur envoie une requête HTTP initiale à l’application.
1. La culture est affectée par l’intergiciel (middleware) de localisation.
1. La `OnGet` méthode dans *_Host. cshtml. cs* rend persistante la culture dans un cookie dans le cadre de la réponse.
1. Le navigateur ouvre une connexion WebSocket pour créer une session Blazor de serveur interactive.
1. L’intergiciel de localisation lit le cookie et assigne la culture.
1. La Blazor session serveur commence par la culture correcte.

#### <a name="provide-ui-to-choose-the-culture"></a>Fournir l’interface utilisateur pour choisir la culture

Pour fournir une interface utilisateur permettant à un utilisateur de sélectionner une culture, il est recommandé d’effectuer une *approche basée sur la redirection* . Le processus est similaire à ce qui se produit dans une application Web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée. L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine. 

L’application conserve la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur. Le contrôleur définit la culture sélectionnée de l’utilisateur dans un cookie et redirige l’utilisateur vers l’URI d’origine.

Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :

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
> Utilisez le `LocalRedirect` résultat de l’action pour empêcher les attaques de redirection ouvertes. Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.

Le composant suivant montre un exemple d’exécution de la redirection initiale lorsque l’utilisateur sélectionne une culture :

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
