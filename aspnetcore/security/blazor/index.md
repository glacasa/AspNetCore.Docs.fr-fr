---
title: ASP.NET authentification et autorisation de base Blazor
author: guardrex
description: Renseignez-vous sur Blazor les scénarios d’authentification et d’autorisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501322"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>ASP.NET authentification et autorisation de base Blazor

Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Pour les conseils dans cet Blazor article qui s’applique à Blazor WebAssembly, le ASP.NET Core WebAssembly modèle version 3.2 ou plus tard est nécessaire. Si vous n’utilisez pas visual Studio version 16.6 Aperçu Blazor 2 ou plus tard, <xref:blazor/get-started>obtenir le dernier modèle WebAssembly en suivant les conseils en .

ASP.NET Core prend en charge la configuration Blazor et la gestion de la sécurité dans les applications.

Les scénarios de Blazor sécurité Blazor diffèrent entre les applications Server et WebAssembly. Étant Blazor donné que les applications Server s’exécutent sur le serveur, les contrôles d’autorisation sont en mesure de déterminer :

* Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).
* Les règles d’accès pour les zones de l’application et les composants.

BlazorLes applications WebAssembly s’exécutent sur le client. L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher. Étant donné que les contrôles côté client peuvent Blazor être modifiés ou contournés par un utilisateur, une application WebAssembly ne peut pas appliquer les règles d’accès à l’autorisation.

[Les conventions d’autorisation Razor Pages](xref:security/authorization/razor-pages-authorization) ne s’appliquent pas aux composants Razor routables. Si un composant Razor non routable est [intégré dans une page,](xref:blazor/integrate-components#render-components-from-a-page-or-view)les conventions d’autorisation de la page affectent indirectement le composant Razor ainsi que le reste du contenu de la page.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas pris en charge dans les composants Razor.

## <a name="authentication"></a>Authentification

Blazorutilise les mécanismes d’authentification ASP.NET core existants pour établir l’identité de l’utilisateur. Le mécanisme exact dépend Blazor de la Blazor façon dont l’application est hébergée, WebAssembly ou Blazor Server.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorAuthentification WebAssembly

Dans Blazor les applications WebAssembly, les contrôles d’authentification peuvent être contournés parce que tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

Ajoutez ce qui suit :

* Une référence de paquet pour [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) au fichier de projet de l’application.
* L’espace `Microsoft.AspNetCore.Components.Authorization` nom du fichier *_Imports.razor* de l’application.

Pour gérer l’authentification, la `AuthenticationStateProvider` mise en œuvre d’un service intégré ou personnalisé est couverte dans les sections suivantes.

Pour plus d’informations sur <xref:security/blazor/webassembly/index>la création d’applications et de configuration, voir .

### <a name="opno-locblazor-server-authentication"></a>BlazorAuthentification du serveur

BlazorLes applications serveur fonctionnent sur une connexion SignalRen temps réel qui est créée à l’aide de . [L’authentification des SignalRapplications basées sur les applications](xref:signalr/authn-and-authz) est gérée lorsque la connexion est établie. L’authentification peut reposer sur un cookie ou un autre jeton du porteur.

Pour plus d’informations sur <xref:security/blazor/server>la création d’applications et de configuration, voir .

## <a name="authenticationstateprovider-service"></a>Service AuthenticationStateProvider

Le `AuthenticationStateProvider` service intégré obtient des données d’état d’authentification de ASP.NET Core' s `HttpContext.User`. C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET fondamentaux existants.

`AuthenticationStateProvider` est le service sous-jacent utilisé par le composant `AuthorizeView` et le composant `CascadingAuthenticationState` pour obtenir l’état d’authentification.

Vous n’utilisez généralement pas `AuthenticationStateProvider` directement. Utilisez le [composant AuthorizeView](#authorizeview-component) ou [\<Task AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approches décrites plus tard dans cet article. Le principal inconvénient de l’utilisation directe de `AuthenticationStateProvider` est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.

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

Si l’application nécessite un `AuthenticationStateProvider` fournisseur `GetAuthenticationStateAsync`personnalisé, implémenter et remplacer :

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

Dans Blazor une application WebAssembly, `CustomAuthStateProvider` le `Main` service est enregistré dans *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Dans Blazor une application `CustomAuthStateProvider` Server, le `Startup.ConfigureServices`service est enregistré dans :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

En `CustomAuthStateProvider` utilisant l’exemple précédent, tous les utilisateurs `mrfibuli`sont authentifiés avec le nom d’utilisateur .

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

Configurez `Task<AuthenticationState>` le paramètre en `AuthorizeRouteView` `CascadingAuthenticationState` cascade à `App` l’aide du composant et des composants du composant *(App.razor*) :

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

Dans Blazor une application WebAssembly, ajoutez des `Program.Main`services pour les options et l’autorisation de :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Dans Blazor une application Server, les services d’options et d’autorisation sont déjà présents, de sorte qu’aucune autre action n’est nécessaire.

## <a name="authorization"></a>Autorisation

Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.

L’accès est généralement accordé ou refusé selon les conditions suivantes :

* Un utilisateur est authentifié (connecté).
* Un utilisateur appartient à un *rôle*.
* Un utilisateur a une *revendication*.
* Une *stratégie* est satisfaite.

Tous ces concepts sont les mêmes que dans une application ASP.NET Core MVC ou Razor Pages. Pour plus d’informations sur la sécurité d’ASP.NET Core, consultez les articles sous [Sécurité et identité dans ASP.NET Core](xref:security/index).

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

Le `AuthorizeView` composant peut être `NavMenu` utilisé dans le composant (*Partagé/NavMenu.razor*) pour afficher un élément de liste (`<li>...</li>`) pour un `NavLink`, mais notez que cette approche supprime seulement l’élément de liste de la sortie rendue. Il n’empêche pas l’utilisateur de naviguer vers le composant.

Le contenu `<Authorized>` `<NotAuthorized>` et les balises peuvent inclure des éléments arbitraires, tels que d’autres composants interactifs.

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

Ces API peuvent être Blazor utilisées Blazor dans les applications Server ou WebAssembly.

Si ni `Roles` ni `Policy` n’est spécifié, `AuthorizeView` utilise la stratégie par défaut.

### <a name="content-displayed-during-asynchronous-authentication"></a>Contenu affiché lors de l’authentification asynchrone

Blazorpermet de déterminer l’état d’authentification *asynchronement.* Le scénario principal de Blazor cette approche est dans les applications WebAssembly qui font une demande à un critère d’évaluation externe pour l’authentification.

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

Cette approche n’est normalement Blazor pas applicable aux applications Server. BlazorLes applications serveur connaissent l’état d’authentification dès que l’état est établi. `Authorizing`le contenu peut Blazor être fourni `AuthorizeView` dans le composant d’une application Server, mais le contenu n’est jamais affiché.

## <a name="authorize-attribute"></a>Attribut [Authorize]

L’attribut `[Authorize]` peut être utilisé dans les composants Razor :

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Utilisation `[Authorize]` uniquement `@page` sur les Blazor composants atteints par le Routeur. L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page. Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez `AuthorizeView` à la place.

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

Le `Router` composant, en `AuthorizeRouteView` conjonction avec le composant, permet à l’application de spécifier le contenu personnalisé si :

* Le contenu est introuvable.
* L’utilisateur ne répond pas à une condition `[Authorize]` appliquée au composant. L’attribut `[Authorize]` est [ `[Authorize]` ](#authorize-attribute) couvert dans la section attribut.
* Une authentification asynchrone est en cours.

Dans le Blazor modèle de `App` projet par défaut Server, le composant (*App.razor*) montre comment définir du contenu personnalisé :

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

Le contenu `<NotFound>` `<NotAuthorized>`de `<Authorizing>` , , et les balises peuvent inclure des éléments arbitraires, tels que d’autres composants interactifs.

Si `<NotAuthorized>` l’élément n’est `AuthorizeRouteView` pas spécifié, le message de repli suivant :

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notification sur les changements d’état de l’authentification

Si l’application détermine que les données sous-jacentes de l’état d’authentification ont changé (par exemple, parce que `NotifyAuthenticationStateChanged` l’utilisateur a signé ou un autre utilisateur a changé ses rôles), un [AuthenticationStateProvider personnalisé](#implement-a-custom-authenticationstateprovider) peut invoquer la méthode sur la `AuthenticationStateProvider` classe de base. Cela prévient les consommateurs des données d’état d’authentification (par exemple, `AuthorizeView`) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.

## <a name="procedural-logic"></a>Logique procédurale

Si l’application est nécessaire pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<AuthenticationState>` pour obtenir le <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur. `Task<AuthenticationState>` peut être combiné avec d’autres services, comme `IAuthorizationService`, pour évaluer les stratégies.

```razor
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
> Dans Blazor un composant d’application WebAssembly, ajoutez les espaces et `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` les espaces nommaux :
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Ces espaces de noms peuvent être fournis à l’échelle mondiale en les ajoutant au fichier *_Imports.razor* de l’application.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorisation Blazor dans les applications WebAssembly

Dans Blazor les applications WebAssembly, les contrôles d’autorisation peuvent être contournés parce que tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**

Pour plus d’informations, <xref:security/blazor/webassembly/index>voir les articles sous .

## <a name="troubleshoot-errors"></a>Résoudre les erreurs

Erreurs courantes :

* **L’autorisation nécessite un paramètre\<en cascade de type Task AuthenticationState>. Envisagez d’utiliser CascadingAuthenticationState pour fournir cela.**

* **`null`valeur est reçue pour`authenticationStateTask`**

Il est probable que le projet n’a pas été créé à l’aide d’un Blazor modèle server avec l’authentification activée. Enveloppez `<CascadingAuthenticationState>` un autour d’une partie de `App` l’arbre d’interface utilisateur, par exemple dans le composant (*App.razor*) comme suit :

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
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* Awesome : Exemples de liens de la communauté [ Blazord’authentification](https://github.com/AdrienTorris/awesome-blazor#authentication)
