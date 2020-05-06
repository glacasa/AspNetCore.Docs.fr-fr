---
title: Authentification Blazor et autorisation ASP.net Core
author: guardrex
description: En savoir Blazor plus sur les scénarios d’authentification et d’autorisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d55880265ed1ceedf8f115412e5ac47309521239
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772893"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>Authentification Blazor et autorisation ASP.net Core

Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Pour les conseils de cet article qui s’appliquent à Blazor webassembly Blazor , le modèle ASP.net Core webassembly version 3,2 ou ultérieure est requis. Si vous n’utilisez pas Visual Studio version 16,6 Preview 2 ou version ultérieure, obtenez Blazor le dernier modèle webassembly en suivant <xref:blazor/get-started>les instructions fournies dans.

ASP.NET Core prend en charge la configuration et la gestion Blazor de la sécurité dans les applications.

Les scénarios de sécurité Blazor diffèrent Blazor entre les applications serveur et webassembly. Étant Blazor donné que les applications serveur s’exécutent sur le serveur, les contrôles d’autorisation peuvent déterminer les éléments suivants :

* Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).
* Les règles d’accès pour les zones de l’application et les composants.

BlazorLes applications webassembly s’exécutent sur le client. L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher. Étant donné que les contrôles côté client peuvent être modifiés ou ignorés par un utilisateur Blazor , une application webassembly ne peut pas appliquer les règles d’accès d’autorisation.

Les conventions d’autorisation des pages ne s' Razor appliquent pas aux composants routables. [ Razor ](xref:security/authorization/razor-pages-authorization) Si un Razor composant non routable est [incorporé dans une page](xref:blazor/integrate-components#render-components-from-a-page-or-view), les conventions d’autorisation de la page affectent Razor indirectement le composant ainsi que le reste du contenu de la page.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas Razor pris en charge dans les composants.

## <a name="authentication"></a>Authentification

Blazorutilise les mécanismes d’authentification ASP.NET Core existants pour établir l’identité de l’utilisateur. Le mécanisme exact dépend de la façon Blazor dont l’application est Blazor hébergée, Blazor webassembly ou Server.

### <a name="blazor-webassembly-authentication"></a>BlazorAuthentification webassembly

Dans Blazor les applications webassembly, les vérifications d’authentification peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

Ajoutez ce qui suit :

* Référence de package pour [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) au fichier projet de l’application.
* `Microsoft.AspNetCore.Components.Authorization` Espace de noms du fichier *_Imports. Razor* de l’application.

Pour gérer l’authentification, l’implémentation d’un service intégré ou `AuthenticationStateProvider` personnalisé est traitée dans les sections suivantes.

Pour plus d’informations sur la création d’applications et <xref:security/blazor/webassembly/index>la configuration, consultez.

### <a name="blazor-server-authentication"></a>BlazorAuthentification du serveur

BlazorLes applications serveur fonctionnent sur une connexion en temps réel créée à l' SignalRaide de. L' [authentification SignalRdans les applications basées](xref:signalr/authn-and-authz) sur est gérée lorsque la connexion est établie. L’authentification peut reposer sur un cookie ou un autre jeton du porteur.

Pour plus d’informations sur la création d’applications et <xref:security/blazor/server/index>la configuration, consultez.

## <a name="authenticationstateprovider-service"></a>Service AuthenticationStateProvider

Le `AuthenticationStateProvider` service intégré obtient les données d’état d’authentification de l' `HttpContext.User`ASP.net core. C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET Core existants.

`AuthenticationStateProvider` est le service sous-jacent utilisé par le composant `AuthorizeView` et le composant `CascadingAuthenticationState` pour obtenir l’état d’authentification.

Vous n’utilisez généralement pas `AuthenticationStateProvider` directement. Utilisez le [composant AuthorizeView](#authorizeview-component) ou [la\<tâche AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approches décrites plus loin dans cet article. Le principal inconvénient de l’utilisation directe de `AuthenticationStateProvider` est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.

Le service `AuthenticationStateProvider` peut fournir les données <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur actuel, comme indiqué dans l’exemple suivant :

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Si `user.Identity.IsAuthenticated` est `true` et parce que l’utilisateur est <xref:System.Security.Claims.ClaimsPrincipal>, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.

Pour plus d’informations sur l’injection de dépendances et les services, consultez <xref:blazor/dependency-injection> et <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implémenter un AuthenticationStateProvider personnalisé

Si l’application requiert un fournisseur personnalisé, implémentez `AuthenticationStateProvider` et substituez : `GetAuthenticationStateAsync`

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

Dans une Blazor application webassembly, `CustomAuthStateProvider` le service est inscrit `Main` dans *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Dans une Blazor application serveur, le `CustomAuthStateProvider` service est inscrit dans `Startup.ConfigureServices`:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

À l' `CustomAuthStateProvider` aide de dans l’exemple précédent, tous les utilisateurs sont authentifiés `mrfibuli`avec le nom d’utilisateur.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Exposer l’état d’authentification comme un paramètre en cascade

Si les données d’état d’authentification sont requises pour la logique procédurale, par exemple lors d’une action déclenchée par l’utilisateur, obtenez les données d’état d’authentification en définissant un paramètre en cascade de type `Task<AuthenticationState>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Si `user.Identity.IsAuthenticated` est `true`, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.

Configurez `Task<AuthenticationState>` le paramètre en cascade à `AuthorizeRouteView` l' `CascadingAuthenticationState` aide des composants `App` et dans le composant (*app. Razor*) :

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Dans une Blazor application webassembly, ajoutez des services pour les options `Program.Main`et l’autorisation pour :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Dans une Blazor application serveur, les services pour les options et l’autorisation sont déjà présents, donc aucune action supplémentaire n’est requise.

## <a name="authorization"></a>Autorisation

Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.

L’accès est généralement accordé ou refusé selon les conditions suivantes :

* Un utilisateur est authentifié (connecté).
* Un utilisateur appartient à un *rôle*.
* Un utilisateur a une *revendication*.
* Une *stratégie* est satisfaite.

Chacun de ces concepts est identique à celui d’une application ASP.NET Core MVC Razor ou pages. Pour plus d’informations sur la sécurité de ASP.NET Core, consultez les articles sous [ASP.net Core sécurité et Identity ](xref:security/index).

## <a name="authorizeview-component"></a>Composant AuthorizeView

Le composant `AuthorizeView` affiche sélectivement l’interface utilisateur en fonction de l’autorisation que l’utilisateur a pour l’afficher. Cette approche est utile lorsque vous devez uniquement *afficher* les données de l’utilisateur et que vous n’avez pas besoin d’utiliser l’identité de l’utilisateur dans la logique procédurale.

Le composant expose une variable `context` de type `AuthenticationState`, que vous pouvez utiliser pour accéder aux informations relatives à l’utilisateur connecté :

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Vous pouvez également fournir un contenu différent à afficher si l’utilisateur n’est pas authentifié :

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

Le `AuthorizeView` composant peut être utilisé dans le `NavMenu` composant (*Shared/NavMenu. Razor*) pour afficher un élément de`<li>...</li>`liste () `NavLink`pour un objet, mais notez que cette approche supprime uniquement l’élément de liste de la sortie rendue. Elle n’empêche pas l’utilisateur de naviguer jusqu’au composant.

Le contenu des `<Authorized>` balises et `<NotAuthorized>` peut inclure des éléments arbitraires, tels que d’autres composants interactifs.

Les conditions d’autorisation, comme les rôles ou les stratégies qui contrôlent les options d’interface utilisateur ou d’accès, sont traitées dans la section [Autorisation](#authorization).

Si les conditions d’autorisation ne sont pas spécifiées, `AuthorizeView` utilise une stratégie par défaut et traite :

* Les utilisateurs authentifiés (connectés) comme étant autorisés.
* Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.

### <a name="role-based-and-policy-based-authorization"></a>Autorisation en fonction du rôle et de la stratégie

Le composant `AuthorizeView` prend en charge l’autorisation *basée sur le rôle* et *basée sur les stratégies*.

Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Pour plus d’informations, consultez <xref:security/authorization/roles>.

Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

L’autorisation basée sur les revendications est un cas spécial d’autorisation basée sur les stratégies. Par exemple, vous pouvez définir une stratégie qui impose aux utilisateurs d’avoir une certaine revendication. Pour plus d’informations, consultez <xref:security/authorization/policies>.

Ces API peuvent être utilisées dans les Blazor applications serveur Blazor ou webassembly.

Si ni `Roles` ni `Policy` n’est spécifié, `AuthorizeView` utilise la stratégie par défaut.

### <a name="content-displayed-during-asynchronous-authentication"></a>Contenu affiché lors de l’authentification asynchrone

Blazorpermet de déterminer l’état d’authentification de *manière asynchrone*. Le scénario principal de cette approche se trouve Blazor dans les applications webassembly qui effectuent une demande à un point de terminaison externe pour l’authentification.

Lorsque l’authentification est en cours, `AuthorizeView` n’affiche aucun contenu par défaut. Pour afficher le contenu pendant que l’authentification se produit, utilisez l’élément `<Authorizing>` :

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Cette approche n’est normalement pas Blazor applicable aux applications serveur. BlazorLes applications serveur connaissent l’état d’authentification dès que l’État est établi. `Authorizing`le contenu peut être fourni dans Blazor le composant d' `AuthorizeView` une application serveur, mais le contenu n’est jamais affiché.

## <a name="authorize-attribute"></a>Attribut [Authorize]

L' `[Authorize]` attribut peut être utilisé dans Razor les composants :

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Utilisez `[Authorize]` uniquement sur `@page` les composants atteints via Blazor le routeur. L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page. Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez `AuthorizeView` à la place.

L’attribut `[Authorize]` prend également en charge l’autorisation en fonction du rôle ou des stratégies. Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Si ni `Roles` ni `Policy` n’est spécifié, `[Authorize]` utilise la stratégie par défaut, qui consiste par défaut à traiter :

* Les utilisateurs authentifiés (connectés) comme étant autorisés.
* Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personnaliser le contenu non autorisé avec le composant Router

Le `Router` composant, conjointement avec le `AuthorizeRouteView` composant, permet à l’application de spécifier du contenu personnalisé dans les cas suivants :

* Le contenu est introuvable.
* L’utilisateur ne répond pas à une condition `[Authorize]` appliquée au composant. L' `[Authorize]` attribut est abordé dans la [ `[Authorize]` section attribut](#authorize-attribute) .
* Une authentification asynchrone est en cours.

Dans le modèle Blazor de projet serveur par défaut `App` , le composant (*app. Razor*) montre comment définir un contenu personnalisé :

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Le contenu des `<NotFound>`balises `<NotAuthorized>`, `<Authorizing>` et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.

Si l' `<NotAuthorized>` élément n’est pas spécifié `AuthorizeRouteView` , le utilise le message de secours suivant :

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notification sur les changements d’état de l’authentification

Si l’application détermine que les données d’état d’authentification sous-jacentes ont changé (par exemple, parce que l’utilisateur s’est déconnecté ou qu’un autre utilisateur a modifié ses rôles), un `NotifyAuthenticationStateChanged` [AuthenticationStateProvider personnalisé](#implement-a-custom-authenticationstateprovider) peut éventuellement appeler la méthode sur la `AuthenticationStateProvider` classe de base. Cela prévient les consommateurs des données d’état d’authentification (par exemple, `AuthorizeView`) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.

## <a name="procedural-logic"></a>Logique procédurale

Si l’application est nécessaire pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<AuthenticationState>` pour obtenir le <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur. `Task<AuthenticationState>` peut être combiné avec d’autres services, comme `IAuthorizationService`, pour évaluer les stratégies.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Dans un Blazor composant d’application webassembly, `Microsoft.AspNetCore.Authorization` ajoutez `Microsoft.AspNetCore.Components.Authorization` les espaces de noms et :
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Ces espaces de noms peuvent être fournis globalement en les ajoutant au fichier *_Imports. Razor* de l’application.

## <a name="authorization-in-blazor-webassembly-apps"></a>Autorisation dans Blazor les applications webassembly

Dans Blazor les applications webassembly, les vérifications d’autorisation peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**

Pour plus d’informations, consultez les articles <xref:security/blazor/webassembly/index>sous.

## <a name="troubleshoot-errors"></a>Résoudre les erreurs

Erreurs courantes :

* **L’autorisation nécessite un paramètre en cascade de type\<Task AuthenticationState>. Envisagez d’utiliser CascadingAuthenticationState pour fournir ce.**

* **`null`la valeur est reçue pour`authenticationStateTask`**

Il est probable que le projet n’a pas été Blazor créé à l’aide d’un modèle de serveur avec l’authentification activée. Encapsulez un autour d’une `<CascadingAuthenticationState>` partie de l’arborescence de l’interface `App` utilisateur, par exemple dans le composant (*app. Razor*) comme suit :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` fournit le paramètre en cascade `Task<AuthenticationState>`, qu’il reçoit à son tour du service d’injection de dépendances `AuthenticationStateProvider` sous-jacent.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Isard Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) exemples de liens vers la communauté d’authentification
