---
title: ASP.NET Principaux Blazor scénarios de sécurité supplémentaires WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661597"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="fcdc6-102">ASP.NET Core Blazor WebAssembly scénarios de sécurité supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fcdc6-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="fcdc6-103">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="fcdc6-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="fcdc6-104">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fcdc6-104">Request additional access tokens</span></span>

<span data-ttu-id="fcdc6-105">La plupart des applications ne nécessitent qu’un jeton d’accès pour interagir avec les ressources protégées qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="fcdc6-106">Dans certains scénarios, une application peut nécessiter plus d’un jeton afin d’interagir avec deux ressources ou plus.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="fcdc6-107">Dans l’exemple suivant, d’autres étendues d’API Microsoft Graph (AAD) d’Azure Active Directory (AAD) sont requises par une application pour lire les données des utilisateurs et envoyer du courrier.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="fcdc6-108">Après avoir ajouté les autorisations d’API Microsoft Graph dans le portail Azure`Program.Main`AAD, les portées supplémentaires sont configurées dans l’application Client ( , *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="fcdc6-109">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de fournir un jeton avec un ensemble donné de portées, comme on le voit dans l’exemple suivant:</span><span class="sxs-lookup"><span data-stu-id="fcdc6-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="fcdc6-110">`TryGetToken`Retourne:</span><span class="sxs-lookup"><span data-stu-id="fcdc6-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="fcdc6-111">`true`avec `token` le pour une utilisation.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="fcdc6-112">`false`si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="fcdc6-113">Gérer les erreurs de demande de jetons</span><span class="sxs-lookup"><span data-stu-id="fcdc6-113">Handle token request errors</span></span>

<span data-ttu-id="fcdc6-114">Lorsqu’une application à page unique (SPA) authentifie un utilisateur utilisant Open ID Connect (OIDC), l’état d’authentification est maintenu localement au sein de la SPA et dans le fournisseur d’identité (IP) sous la forme d’un cookie de session défini par l’utilisateur fournissant ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="fcdc6-115">Les jetons que l’IP émet pour l’utilisateur sont généralement valables pour de courtes périodes de temps, environ une heure normalement, de sorte que l’application client doit régulièrement aller chercher de nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="fcdc6-116">Dans le cas contraire, l’utilisateur serait déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="fcdc6-117">Dans la plupart des cas, les clients d’OIDC sont en mesure de fournir de nouveaux jetons sans obliger l’utilisateur à s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservé dans la propriété intellectuelle.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="fcdc6-118">Il y a certains cas dans lesquels le client ne peut pas obtenir un jeton sans interaction utilisateur, par exemple, quand pour une raison quelconque l’utilisateur se connecte explicitement à partir de l’IP.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="fcdc6-119">Ce scénario se produit `https://login.microsoftonline.com` si un utilisateur visite et se connecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout signe que le client détient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="fcdc6-120">En outre, le client n’est pas en mesure de fournir un nouveau jeton sans interaction utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="fcdc6-121">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="fcdc6-122">Ils font partie de la nature des APE.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="fcdc6-123">Un SPA utilisant des cookies ne parvient pas non plus à appeler un serveur API si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="fcdc6-124">Lorsqu’une application effectue des appels API vers des ressources protégées, vous devez être conscient des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="fcdc6-125">Pour fournir un nouveau jeton d’accès pour appeler l’API, l’utilisateur pourrait être tenu de s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="fcdc6-126">Même si le client a un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="fcdc6-127">Lorsque l’application demande un jeton, il y a deux résultats possibles :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="fcdc6-128">La demande réussit, et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="fcdc6-129">La demande échoue, et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="fcdc6-130">Lorsqu’une demande symbolique échoue, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="fcdc6-131">Plusieurs approches existent avec des niveaux croissants de complexité :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="fcdc6-132">Stockez l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-132">Store the current page state in session storage.</span></span> <span data-ttu-id="fcdc6-133">Pendant `OnInitializeAsync`, vérifiez si l’état peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="fcdc6-134">Ajoutez un paramètre de chaîne de requête et utilisez-le comme moyen de signaler à l’application qu’elle doit réhydrater l’état précédemment enregistré.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="fcdc6-135">Ajoutez un paramètre de chaîne de requête avec un identifiant unique pour stocker des données dans le stockage de session sans risquer de collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="fcdc6-136">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-136">The following example shows how to:</span></span>

* <span data-ttu-id="fcdc6-137">Préserver l’état avant de rediriger vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="fcdc6-138">Récupérez l’état précédent par la suite authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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
            await httpClient.PostAsJsonAsync("Save", User);
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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="fcdc6-139">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="fcdc6-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="fcdc6-140">Au cours d’une opération d’authentification, il y a des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur ne soit redirigé vers l’IP.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="fcdc6-141">Cela peut être le cas lorsque vous utilisez quelque chose comme un conteneur d’état et que vous voulez restaurer l’état après l’authentification réussit.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="fcdc6-142">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour préserver l’état spécifique à l’application ou une référence à celui-ci et restaurer cet état une fois que l’opération d’authentification terminée avec succès.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="fcdc6-143">`Authentication`composant (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="fcdc6-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="fcdc6-144">Personnaliser les itinéraires d’applications</span><span class="sxs-lookup"><span data-stu-id="fcdc6-144">Customize app routes</span></span>

<span data-ttu-id="fcdc6-145">Par défaut, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter différents états d’authentification.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="fcdc6-146">Routage</span><span class="sxs-lookup"><span data-stu-id="fcdc6-146">Route</span></span>                            | <span data-ttu-id="fcdc6-147">Objectif</span><span class="sxs-lookup"><span data-stu-id="fcdc6-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="fcdc6-148">Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="fcdc6-149">Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="fcdc6-150">Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="fcdc6-151">Déclenche une opération de signalisation.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="fcdc6-152">Gère le résultat d’une opération d’affichage.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="fcdc6-153">Affiche des messages d’erreur lorsque l’opération de signalisation échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="fcdc6-154">Indique que l’utilisateur a réussi logout.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="fcdc6-155">Déclenche une opération pour modifier le profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="fcdc6-156">Déclenche une opération pour enregistrer un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="fcdc6-157">Les itinéraires indiqués dans le `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`tableau précédent sont configurables via .</span><span class="sxs-lookup"><span data-stu-id="fcdc6-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="fcdc6-158">Lorsque vous définissez des options pour fournir des itinéraires personnalisés, confirmez que l’application dispose d’un itinéraire qui gère chaque chemin.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="fcdc6-159">Dans l’exemple suivant, tous les `/security`chemins sont préfixés avec .</span><span class="sxs-lookup"><span data-stu-id="fcdc6-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="fcdc6-160">`Authentication`composant (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="fcdc6-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="fcdc6-161">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="fcdc6-162">Si l’exigence exige des chemins complètement différents, définissez les itinéraires comme décrit précédemment et rendre le `RemoteAuthenticatorView` paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="fcdc6-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="fcdc6-163">Vous êtes autorisé à casser l’interface utilisateur en différentes pages si vous choisissez de le faire.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="fcdc6-164">Personnaliser l’interface utilisateur d’authentification</span><span class="sxs-lookup"><span data-stu-id="fcdc6-164">Customize the authentication user interface</span></span>

<span data-ttu-id="fcdc6-165">`RemoteAuthenticatorView`comprend un ensemble par défaut de pièces d’interface utilisateur pour chaque état d’authentification.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="fcdc6-166">Chaque état peut être personnalisé en `RenderFragment`passant dans une coutume .</span><span class="sxs-lookup"><span data-stu-id="fcdc6-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="fcdc6-167">Pour personnaliser le texte affiché pendant le processus `RemoteAuthenticatorView` de connexion initial, peut changer les éléments suivants.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="fcdc6-168">`Authentication`composant (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="fcdc6-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="fcdc6-169">Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification montré dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="fcdc6-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="fcdc6-170">Routage</span><span class="sxs-lookup"><span data-stu-id="fcdc6-170">Route</span></span>                            | <span data-ttu-id="fcdc6-171">Fragment</span><span class="sxs-lookup"><span data-stu-id="fcdc6-171">Fragment</span></span>                |
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
