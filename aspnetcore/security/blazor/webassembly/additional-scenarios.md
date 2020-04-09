---
title: ASP.NET Principaux Blazor scénarios de sécurité supplémentaires WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501125"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly scénarios de sécurité supplémentaires

Par [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Demander des jetons d’accès supplémentaires

La plupart des applications ne nécessitent qu’un jeton d’accès pour interagir avec les ressources protégées qu’elles utilisent. Dans certains scénarios, une application peut nécessiter plus d’un jeton afin d’interagir avec deux ressources ou plus.

Dans l’exemple suivant, d’autres étendues d’API Microsoft Graph (AAD) d’Azure Active Directory (AAD) sont requises par une application pour lire les données des utilisateurs et envoyer du courrier. Après avoir ajouté les autorisations d’API Microsoft Graph dans le portail Azure`Program.Main`AAD, les portées supplémentaires sont configurées dans l’application Client ( , *Program.cs*) :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de fournir un jeton avec un ensemble donné de portées, comme on le voit dans l’exemple suivant:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`Retourne:

* `true`avec `token` le pour une utilisation.
* `false`si le jeton n’est pas récupéré.

## <a name="handle-token-request-errors"></a>Gérer les erreurs de demande de jetons

Lorsqu’une application à page unique (SPA) authentifie un utilisateur utilisant Open ID Connect (OIDC), l’état d’authentification est maintenu localement au sein de la SPA et dans le fournisseur d’identité (IP) sous la forme d’un cookie de session défini par l’utilisateur fournissant ses informations d’identification.

Les jetons que l’IP émet pour l’utilisateur sont généralement valables pour de courtes périodes de temps, environ une heure normalement, de sorte que l’application client doit régulièrement aller chercher de nouveaux jetons. Dans le cas contraire, l’utilisateur serait déconnecté après l’expiration des jetons accordés. Dans la plupart des cas, les clients d’OIDC sont en mesure de fournir de nouveaux jetons sans obliger l’utilisateur à s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservé dans la propriété intellectuelle.

Il y a certains cas dans lesquels le client ne peut pas obtenir un jeton sans interaction utilisateur, par exemple, quand pour une raison quelconque l’utilisateur se connecte explicitement à partir de l’IP. Ce scénario se produit `https://login.microsoftonline.com` si un utilisateur visite et se connecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout signe que le client détient peut ne plus être valide. En outre, le client n’est pas en mesure de fournir un nouveau jeton sans interaction utilisateur après l’expiration du jeton actuel.

Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons. Ils font partie de la nature des APE. Un SPA utilisant des cookies ne parvient pas non plus à appeler un serveur API si le cookie d’authentification est supprimé.

Lorsqu’une application effectue des appels API vers des ressources protégées, vous devez être conscient des éléments suivants :

* Pour fournir un nouveau jeton d’accès pour appeler l’API, l’utilisateur pourrait être tenu de s’authentifier à nouveau.
* Même si le client a un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.

Lorsque l’application demande un jeton, il y a deux résultats possibles :

* La demande réussit, et l’application a un jeton valide.
* La demande échoue, et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.

Lorsqu’une demande symbolique échoue, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection. Plusieurs approches existent avec des niveaux croissants de complexité :

* Stockez l’état actuel de la page dans le stockage de session. Pendant `OnInitializeAsync`, vérifiez si l’état peut être restauré avant de continuer.
* Ajoutez un paramètre de chaîne de requête et utilisez-le comme moyen de signaler à l’application qu’elle doit réhydrater l’état précédemment enregistré.
* Ajoutez un paramètre de chaîne de requête avec un identifiant unique pour stocker des données dans le stockage de session sans risquer de collisions avec d’autres éléments.

L’exemple suivant montre comment :

* Préserver l’état avant de rediriger vers la page de connexion.
* Récupérez l’état précédent par la suite authentification à l’aide du paramètre de chaîne de requête.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>Enregistrer l’état de l’application avant une opération d’authentification

Au cours d’une opération d’authentification, il y a des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur ne soit redirigé vers l’IP. Cela peut être le cas lorsque vous utilisez quelque chose comme un conteneur d’état et que vous voulez restaurer l’état après l’authentification réussit. Vous pouvez utiliser un objet d’état d’authentification personnalisé pour préserver l’état spécifique à l’application ou une référence à celui-ci et restaurer cet état une fois que l’opération d’authentification terminée avec succès.

`Authentication`composant (*Pages/Authentication.razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a>Personnaliser les itinéraires d’applications

Par défaut, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter différents états d’authentification.

| Routage                            | Objectif |
| -------------------------------- | ------- |
| `authentication/login`           | Déclenche une opération de connexion. |
| `authentication/login-callback`  | Gère le résultat de toute opération de connexion. |
| `authentication/login-failed`    | Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque. |
| `authentication/logout`          | Déclenche une opération de signalisation. |
| `authentication/logout-callback` | Gère le résultat d’une opération d’affichage. |
| `authentication/logout-failed`   | Affiche des messages d’erreur lorsque l’opération de signalisation échoue pour une raison quelconque. |
| `authentication/logged-out`      | Indique que l’utilisateur a réussi logout. |
| `authentication/profile`         | Déclenche une opération pour modifier le profil utilisateur. |
| `authentication/register`        | Déclenche une opération pour enregistrer un nouvel utilisateur. |

Les itinéraires indiqués dans le `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`tableau précédent sont configurables via . Lorsque vous définissez des options pour fournir des itinéraires personnalisés, confirmez que l’application dispose d’un itinéraire qui gère chaque chemin.

Dans l’exemple suivant, tous les `/security`chemins sont préfixés avec .

`Authentication`composant (*Pages/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*) :

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Si l’exigence exige des chemins complètement différents, définissez les itinéraires comme décrit précédemment et rendre le `RemoteAuthenticatorView` paramètre d’action explicite :

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Vous êtes autorisé à casser l’interface utilisateur en différentes pages si vous choisissez de le faire.

## <a name="customize-the-authentication-user-interface"></a>Personnaliser l’interface utilisateur d’authentification

`RemoteAuthenticatorView`comprend un ensemble par défaut de pièces d’interface utilisateur pour chaque état d’authentification. Chaque état peut être personnalisé en `RenderFragment`passant dans une coutume . Pour personnaliser le texte affiché pendant le processus `RemoteAuthenticatorView` de connexion initial, peut changer les éléments suivants.

`Authentication`composant (*Pages/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification montré dans le tableau suivant.

| Routage                            | Fragment                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
