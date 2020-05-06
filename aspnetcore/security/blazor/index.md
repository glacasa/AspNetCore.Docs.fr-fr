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
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="58b82-103">Authentification Blazor et autorisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="58b82-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="58b82-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="58b82-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="58b82-105">Pour les conseils de cet article qui s’appliquent à Blazor webassembly Blazor , le modèle ASP.net Core webassembly version 3,2 ou ultérieure est requis.</span><span class="sxs-lookup"><span data-stu-id="58b82-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="58b82-106">Si vous n’utilisez pas Visual Studio version 16,6 Preview 2 ou version ultérieure, obtenez Blazor le dernier modèle webassembly en suivant <xref:blazor/get-started>les instructions fournies dans.</span><span class="sxs-lookup"><span data-stu-id="58b82-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="58b82-107">ASP.NET Core prend en charge la configuration et la gestion Blazor de la sécurité dans les applications.</span><span class="sxs-lookup"><span data-stu-id="58b82-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="58b82-108">Les scénarios de sécurité Blazor diffèrent Blazor entre les applications serveur et webassembly.</span><span class="sxs-lookup"><span data-stu-id="58b82-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="58b82-109">Étant Blazor donné que les applications serveur s’exécutent sur le serveur, les contrôles d’autorisation peuvent déterminer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="58b82-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="58b82-110">Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).</span><span class="sxs-lookup"><span data-stu-id="58b82-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="58b82-111">Les règles d’accès pour les zones de l’application et les composants.</span><span class="sxs-lookup"><span data-stu-id="58b82-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="58b82-112">Les applications webassembly s’exécutent sur le client.</span><span class="sxs-lookup"><span data-stu-id="58b82-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="58b82-113">L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher.</span><span class="sxs-lookup"><span data-stu-id="58b82-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="58b82-114">Étant donné que les contrôles côté client peuvent être modifiés ou ignorés par un utilisateur Blazor , une application webassembly ne peut pas appliquer les règles d’accès d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="58b82-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="58b82-115">Les conventions d’autorisation des pages ne s' Razor appliquent pas aux composants routables. [ Razor ](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="58b82-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="58b82-116">Si un Razor composant non routable est [incorporé dans une page](xref:blazor/integrate-components#render-components-from-a-page-or-view), les conventions d’autorisation de la page affectent Razor indirectement le composant ainsi que le reste du contenu de la page.</span><span class="sxs-lookup"><span data-stu-id="58b82-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="58b82-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas Razor pris en charge dans les composants.</span><span class="sxs-lookup"><span data-stu-id="58b82-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="58b82-118">Authentification</span><span class="sxs-lookup"><span data-stu-id="58b82-118">Authentication</span></span>

Blazor<span data-ttu-id="58b82-119">utilise les mécanismes d’authentification ASP.NET Core existants pour établir l’identité de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="58b82-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="58b82-120">Le mécanisme exact dépend de la façon Blazor dont l’application est Blazor hébergée, Blazor webassembly ou Server.</span><span class="sxs-lookup"><span data-stu-id="58b82-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="58b82-121">Authentification webassembly</span><span class="sxs-lookup"><span data-stu-id="58b82-121"> WebAssembly authentication</span></span>

<span data-ttu-id="58b82-122">Dans Blazor les applications webassembly, les vérifications d’authentification peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="58b82-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="58b82-123">Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="58b82-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="58b82-124">Ajoutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="58b82-124">Add the following:</span></span>

* <span data-ttu-id="58b82-125">Référence de package pour [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) au fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="58b82-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="58b82-126">`Microsoft.AspNetCore.Components.Authorization` Espace de noms du fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="58b82-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="58b82-127">Pour gérer l’authentification, l’implémentation d’un service intégré ou `AuthenticationStateProvider` personnalisé est traitée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="58b82-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="58b82-128">Pour plus d’informations sur la création d’applications et <xref:security/blazor/webassembly/index>la configuration, consultez.</span><span class="sxs-lookup"><span data-stu-id="58b82-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="58b82-129">Authentification du serveur</span><span class="sxs-lookup"><span data-stu-id="58b82-129"> Server authentication</span></span>

Blazor<span data-ttu-id="58b82-130">Les applications serveur fonctionnent sur une connexion en temps réel créée à l' SignalRaide de.</span><span class="sxs-lookup"><span data-stu-id="58b82-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="58b82-131">L' [authentification SignalRdans les applications basées](xref:signalr/authn-and-authz) sur est gérée lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="58b82-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="58b82-132">L’authentification peut reposer sur un cookie ou un autre jeton du porteur.</span><span class="sxs-lookup"><span data-stu-id="58b82-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="58b82-133">Pour plus d’informations sur la création d’applications et <xref:security/blazor/server/index>la configuration, consultez.</span><span class="sxs-lookup"><span data-stu-id="58b82-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="58b82-134">Service AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="58b82-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="58b82-135">Le `AuthenticationStateProvider` service intégré obtient les données d’état d’authentification de l' `HttpContext.User`ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="58b82-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="58b82-136">C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET Core existants.</span><span class="sxs-lookup"><span data-stu-id="58b82-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="58b82-137">`AuthenticationStateProvider` est le service sous-jacent utilisé par le composant `AuthorizeView` et le composant `CascadingAuthenticationState` pour obtenir l’état d’authentification.</span><span class="sxs-lookup"><span data-stu-id="58b82-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="58b82-138">Vous n’utilisez généralement pas `AuthenticationStateProvider` directement.</span><span class="sxs-lookup"><span data-stu-id="58b82-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="58b82-139">Utilisez le [composant AuthorizeView](#authorizeview-component) ou [la\<tâche AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approches décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="58b82-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="58b82-140">Le principal inconvénient de l’utilisation directe de `AuthenticationStateProvider` est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="58b82-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="58b82-141">Le service `AuthenticationStateProvider` peut fournir les données <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur actuel, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="58b82-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="58b82-142">Si `user.Identity.IsAuthenticated` est `true` et parce que l’utilisateur est <xref:System.Security.Claims.ClaimsPrincipal>, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="58b82-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="58b82-143">Pour plus d’informations sur l’injection de dépendances et les services, consultez <xref:blazor/dependency-injection> et <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="58b82-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="58b82-144">Implémenter un AuthenticationStateProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="58b82-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="58b82-145">Si l’application requiert un fournisseur personnalisé, implémentez `AuthenticationStateProvider` et substituez : `GetAuthenticationStateAsync`</span><span class="sxs-lookup"><span data-stu-id="58b82-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="58b82-146">Dans une Blazor application webassembly, `CustomAuthStateProvider` le service est inscrit `Main` dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="58b82-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="58b82-147">Dans une Blazor application serveur, le `CustomAuthStateProvider` service est inscrit dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58b82-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="58b82-148">À l' `CustomAuthStateProvider` aide de dans l’exemple précédent, tous les utilisateurs sont authentifiés `mrfibuli`avec le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="58b82-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="58b82-149">Exposer l’état d’authentification comme un paramètre en cascade</span><span class="sxs-lookup"><span data-stu-id="58b82-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="58b82-150">Si les données d’état d’authentification sont requises pour la logique procédurale, par exemple lors d’une action déclenchée par l’utilisateur, obtenez les données d’état d’authentification en définissant un paramètre en cascade de type `Task<AuthenticationState>` :</span><span class="sxs-lookup"><span data-stu-id="58b82-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="58b82-151">Si `user.Identity.IsAuthenticated` est `true`, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="58b82-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="58b82-152">Configurez `Task<AuthenticationState>` le paramètre en cascade à `AuthorizeRouteView` l' `CascadingAuthenticationState` aide des composants `App` et dans le composant (*app. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="58b82-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="58b82-153">Dans une Blazor application webassembly, ajoutez des services pour les options `Program.Main`et l’autorisation pour :</span><span class="sxs-lookup"><span data-stu-id="58b82-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="58b82-154">Dans une Blazor application serveur, les services pour les options et l’autorisation sont déjà présents, donc aucune action supplémentaire n’est requise.</span><span class="sxs-lookup"><span data-stu-id="58b82-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="58b82-155">Autorisation</span><span class="sxs-lookup"><span data-stu-id="58b82-155">Authorization</span></span>

<span data-ttu-id="58b82-156">Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.</span><span class="sxs-lookup"><span data-stu-id="58b82-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="58b82-157">L’accès est généralement accordé ou refusé selon les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="58b82-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="58b82-158">Un utilisateur est authentifié (connecté).</span><span class="sxs-lookup"><span data-stu-id="58b82-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="58b82-159">Un utilisateur appartient à un *rôle*.</span><span class="sxs-lookup"><span data-stu-id="58b82-159">A user is in a *role*.</span></span>
* <span data-ttu-id="58b82-160">Un utilisateur a une *revendication*.</span><span class="sxs-lookup"><span data-stu-id="58b82-160">A user has a *claim*.</span></span>
* <span data-ttu-id="58b82-161">Une *stratégie* est satisfaite.</span><span class="sxs-lookup"><span data-stu-id="58b82-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="58b82-162">Chacun de ces concepts est identique à celui d’une application ASP.NET Core MVC Razor ou pages.</span><span class="sxs-lookup"><span data-stu-id="58b82-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="58b82-163">Pour plus d’informations sur la sécurité de ASP.NET Core, consultez les articles sous [ASP.net Core sécurité et Identity ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="58b82-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="58b82-164">Composant AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="58b82-164">AuthorizeView component</span></span>

<span data-ttu-id="58b82-165">Le composant `AuthorizeView` affiche sélectivement l’interface utilisateur en fonction de l’autorisation que l’utilisateur a pour l’afficher.</span><span class="sxs-lookup"><span data-stu-id="58b82-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="58b82-166">Cette approche est utile lorsque vous devez uniquement *afficher* les données de l’utilisateur et que vous n’avez pas besoin d’utiliser l’identité de l’utilisateur dans la logique procédurale.</span><span class="sxs-lookup"><span data-stu-id="58b82-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="58b82-167">Le composant expose une variable `context` de type `AuthenticationState`, que vous pouvez utiliser pour accéder aux informations relatives à l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="58b82-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="58b82-168">Vous pouvez également fournir un contenu différent à afficher si l’utilisateur n’est pas authentifié :</span><span class="sxs-lookup"><span data-stu-id="58b82-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="58b82-169">Le `AuthorizeView` composant peut être utilisé dans le `NavMenu` composant (*Shared/NavMenu. Razor*) pour afficher un élément de`<li>...</li>`liste () `NavLink`pour un objet, mais notez que cette approche supprime uniquement l’élément de liste de la sortie rendue.</span><span class="sxs-lookup"><span data-stu-id="58b82-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="58b82-170">Elle n’empêche pas l’utilisateur de naviguer jusqu’au composant.</span><span class="sxs-lookup"><span data-stu-id="58b82-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="58b82-171">Le contenu des `<Authorized>` balises et `<NotAuthorized>` peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="58b82-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="58b82-172">Les conditions d’autorisation, comme les rôles ou les stratégies qui contrôlent les options d’interface utilisateur ou d’accès, sont traitées dans la section [Autorisation](#authorization).</span><span class="sxs-lookup"><span data-stu-id="58b82-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="58b82-173">Si les conditions d’autorisation ne sont pas spécifiées, `AuthorizeView` utilise une stratégie par défaut et traite :</span><span class="sxs-lookup"><span data-stu-id="58b82-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="58b82-174">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="58b82-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="58b82-175">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="58b82-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="58b82-176">Autorisation en fonction du rôle et de la stratégie</span><span class="sxs-lookup"><span data-stu-id="58b82-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="58b82-177">Le composant `AuthorizeView` prend en charge l’autorisation *basée sur le rôle* et *basée sur les stratégies*.</span><span class="sxs-lookup"><span data-stu-id="58b82-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="58b82-178">Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :</span><span class="sxs-lookup"><span data-stu-id="58b82-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="58b82-179">Pour plus d’informations, consultez <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="58b82-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="58b82-180">Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :</span><span class="sxs-lookup"><span data-stu-id="58b82-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="58b82-181">L’autorisation basée sur les revendications est un cas spécial d’autorisation basée sur les stratégies.</span><span class="sxs-lookup"><span data-stu-id="58b82-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="58b82-182">Par exemple, vous pouvez définir une stratégie qui impose aux utilisateurs d’avoir une certaine revendication.</span><span class="sxs-lookup"><span data-stu-id="58b82-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="58b82-183">Pour plus d’informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="58b82-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="58b82-184">Ces API peuvent être utilisées dans les Blazor applications serveur Blazor ou webassembly.</span><span class="sxs-lookup"><span data-stu-id="58b82-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="58b82-185">Si ni `Roles` ni `Policy` n’est spécifié, `AuthorizeView` utilise la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="58b82-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="58b82-186">Contenu affiché lors de l’authentification asynchrone</span><span class="sxs-lookup"><span data-stu-id="58b82-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="58b82-187">permet de déterminer l’état d’authentification de *manière asynchrone*.</span><span class="sxs-lookup"><span data-stu-id="58b82-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="58b82-188">Le scénario principal de cette approche se trouve Blazor dans les applications webassembly qui effectuent une demande à un point de terminaison externe pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="58b82-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="58b82-189">Lorsque l’authentification est en cours, `AuthorizeView` n’affiche aucun contenu par défaut.</span><span class="sxs-lookup"><span data-stu-id="58b82-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="58b82-190">Pour afficher le contenu pendant que l’authentification se produit, utilisez l’élément `<Authorizing>` :</span><span class="sxs-lookup"><span data-stu-id="58b82-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="58b82-191">Cette approche n’est normalement pas Blazor applicable aux applications serveur.</span><span class="sxs-lookup"><span data-stu-id="58b82-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="58b82-192">Les applications serveur connaissent l’état d’authentification dès que l’État est établi.</span><span class="sxs-lookup"><span data-stu-id="58b82-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="58b82-193">`Authorizing`le contenu peut être fourni dans Blazor le composant d' `AuthorizeView` une application serveur, mais le contenu n’est jamais affiché.</span><span class="sxs-lookup"><span data-stu-id="58b82-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="58b82-194">Attribut [Authorize]</span><span class="sxs-lookup"><span data-stu-id="58b82-194">[Authorize] attribute</span></span>

<span data-ttu-id="58b82-195">L' `[Authorize]` attribut peut être utilisé dans Razor les composants :</span><span class="sxs-lookup"><span data-stu-id="58b82-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="58b82-196">Utilisez `[Authorize]` uniquement sur `@page` les composants atteints via Blazor le routeur.</span><span class="sxs-lookup"><span data-stu-id="58b82-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="58b82-197">L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page.</span><span class="sxs-lookup"><span data-stu-id="58b82-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="58b82-198">Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez `AuthorizeView` à la place.</span><span class="sxs-lookup"><span data-stu-id="58b82-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="58b82-199">L’attribut `[Authorize]` prend également en charge l’autorisation en fonction du rôle ou des stratégies.</span><span class="sxs-lookup"><span data-stu-id="58b82-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="58b82-200">Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :</span><span class="sxs-lookup"><span data-stu-id="58b82-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="58b82-201">Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :</span><span class="sxs-lookup"><span data-stu-id="58b82-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="58b82-202">Si ni `Roles` ni `Policy` n’est spécifié, `[Authorize]` utilise la stratégie par défaut, qui consiste par défaut à traiter :</span><span class="sxs-lookup"><span data-stu-id="58b82-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="58b82-203">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="58b82-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="58b82-204">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="58b82-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="58b82-205">Personnaliser le contenu non autorisé avec le composant Router</span><span class="sxs-lookup"><span data-stu-id="58b82-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="58b82-206">Le `Router` composant, conjointement avec le `AuthorizeRouteView` composant, permet à l’application de spécifier du contenu personnalisé dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="58b82-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="58b82-207">Le contenu est introuvable.</span><span class="sxs-lookup"><span data-stu-id="58b82-207">Content isn't found.</span></span>
* <span data-ttu-id="58b82-208">L’utilisateur ne répond pas à une condition `[Authorize]` appliquée au composant.</span><span class="sxs-lookup"><span data-stu-id="58b82-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="58b82-209">L' `[Authorize]` attribut est abordé dans la [ `[Authorize]` section attribut](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="58b82-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="58b82-210">Une authentification asynchrone est en cours.</span><span class="sxs-lookup"><span data-stu-id="58b82-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="58b82-211">Dans le modèle Blazor de projet serveur par défaut `App` , le composant (*app. Razor*) montre comment définir un contenu personnalisé :</span><span class="sxs-lookup"><span data-stu-id="58b82-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="58b82-212">Le contenu des `<NotFound>`balises `<NotAuthorized>`, `<Authorizing>` et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="58b82-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="58b82-213">Si l' `<NotAuthorized>` élément n’est pas spécifié `AuthorizeRouteView` , le utilise le message de secours suivant :</span><span class="sxs-lookup"><span data-stu-id="58b82-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="58b82-214">Notification sur les changements d’état de l’authentification</span><span class="sxs-lookup"><span data-stu-id="58b82-214">Notification about authentication state changes</span></span>

<span data-ttu-id="58b82-215">Si l’application détermine que les données d’état d’authentification sous-jacentes ont changé (par exemple, parce que l’utilisateur s’est déconnecté ou qu’un autre utilisateur a modifié ses rôles), un `NotifyAuthenticationStateChanged` [AuthenticationStateProvider personnalisé](#implement-a-custom-authenticationstateprovider) peut éventuellement appeler la méthode sur la `AuthenticationStateProvider` classe de base.</span><span class="sxs-lookup"><span data-stu-id="58b82-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="58b82-216">Cela prévient les consommateurs des données d’état d’authentification (par exemple, `AuthorizeView`) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.</span><span class="sxs-lookup"><span data-stu-id="58b82-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="58b82-217">Logique procédurale</span><span class="sxs-lookup"><span data-stu-id="58b82-217">Procedural logic</span></span>

<span data-ttu-id="58b82-218">Si l’application est nécessaire pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<AuthenticationState>` pour obtenir le <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="58b82-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="58b82-219">`Task<AuthenticationState>` peut être combiné avec d’autres services, comme `IAuthorizationService`, pour évaluer les stratégies.</span><span class="sxs-lookup"><span data-stu-id="58b82-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="58b82-220">Dans un Blazor composant d’application webassembly, `Microsoft.AspNetCore.Authorization` ajoutez `Microsoft.AspNetCore.Components.Authorization` les espaces de noms et :</span><span class="sxs-lookup"><span data-stu-id="58b82-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="58b82-221">Ces espaces de noms peuvent être fournis globalement en les ajoutant au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="58b82-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="58b82-222">Autorisation dans Blazor les applications webassembly</span><span class="sxs-lookup"><span data-stu-id="58b82-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="58b82-223">Dans Blazor les applications webassembly, les vérifications d’autorisation peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="58b82-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="58b82-224">Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="58b82-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="58b82-225">**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**</span><span class="sxs-lookup"><span data-stu-id="58b82-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="58b82-226">Pour plus d’informations, consultez les articles <xref:security/blazor/webassembly/index>sous.</span><span class="sxs-lookup"><span data-stu-id="58b82-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="58b82-227">Résoudre les erreurs</span><span class="sxs-lookup"><span data-stu-id="58b82-227">Troubleshoot errors</span></span>

<span data-ttu-id="58b82-228">Erreurs courantes :</span><span class="sxs-lookup"><span data-stu-id="58b82-228">Common errors:</span></span>

* <span data-ttu-id="58b82-229">**L’autorisation nécessite un paramètre en cascade de type\<Task AuthenticationState>. Envisagez d’utiliser CascadingAuthenticationState pour fournir ce.**</span><span class="sxs-lookup"><span data-stu-id="58b82-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="58b82-230">**`null`la valeur est reçue pour`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="58b82-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="58b82-231">Il est probable que le projet n’a pas été Blazor créé à l’aide d’un modèle de serveur avec l’authentification activée.</span><span class="sxs-lookup"><span data-stu-id="58b82-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="58b82-232">Encapsulez un autour d’une `<CascadingAuthenticationState>` partie de l’arborescence de l’interface `App` utilisateur, par exemple dans le composant (*app. Razor*) comme suit :</span><span class="sxs-lookup"><span data-stu-id="58b82-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="58b82-233">`CascadingAuthenticationState` fournit le paramètre en cascade `Task<AuthenticationState>`, qu’il reçoit à son tour du service d’injection de dépendances `AuthenticationStateProvider` sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="58b82-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58b82-234">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="58b82-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="58b82-235">[Isard Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) exemples de liens vers la communauté d’authentification</span><span class="sxs-lookup"><span data-stu-id="58b82-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
