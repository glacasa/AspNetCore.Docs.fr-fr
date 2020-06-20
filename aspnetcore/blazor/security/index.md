---
title: BlazorAuthentification et autorisation ASP.net Core
author: guardrex
description: En savoir plus sur Blazor les scénarios d’authentification et d’autorisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: 8c5e6c6ba741cae2634e2852e9ca141a0a7c33ce
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103662"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="1c9ae-103">BlazorAuthentification et autorisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="1c9ae-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="1c9ae-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1c9ae-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1c9ae-105">ASP.NET Core prend en charge la configuration et la gestion de la sécurité dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="1c9ae-106">Les scénarios de sécurité diffèrent entre Blazor les applications serveur et Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="1c9ae-107">Étant donné que Blazor les applications serveur s’exécutent sur le serveur, les contrôles d’autorisation peuvent déterminer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="1c9ae-108">Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).</span><span class="sxs-lookup"><span data-stu-id="1c9ae-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="1c9ae-109">Les règles d’accès pour les zones de l’application et les composants.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-109">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="1c9ae-110">Les applications webassembly s’exécutent sur le client.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-110"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="1c9ae-111">L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="1c9ae-112">Étant donné que les contrôles côté client peuvent être modifiés ou ignorés par un utilisateur, une Blazor application Webassembly ne peut pas appliquer les règles d’accès d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="1c9ae-113">Les [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization) ne s’appliquent pas aux composants routables Razor .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="1c9ae-114">Si un composant non routable Razor est [incorporé dans une page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), les conventions d’autorisation de la page affectent indirectement le Razor composant ainsi que le reste du contenu de la page.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="1c9ae-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas pris en charge dans les Razor composants.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="1c9ae-116">Authentification</span><span class="sxs-lookup"><span data-stu-id="1c9ae-116">Authentication</span></span>

Blazor<span data-ttu-id="1c9ae-117">utilise les mécanismes d’authentification ASP.NET Core existants pour établir l’identité de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="1c9ae-118">Le mécanisme exact dépend de la façon dont l' Blazor application est hébergée, Blazor webassembly ou Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="1c9ae-119">Authentification webassembly</span><span class="sxs-lookup"><span data-stu-id="1c9ae-119"> WebAssembly authentication</span></span>

<span data-ttu-id="1c9ae-120">Dans Blazor les applications Webassembly, les vérifications d’authentification peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="1c9ae-121">Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="1c9ae-122">Ajoutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-122">Add the following:</span></span>

* <span data-ttu-id="1c9ae-123">Référence de package pour [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) au fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-123">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="1c9ae-124">`Microsoft.AspNetCore.Components.Authorization`Espace de noms du fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="1c9ae-125">Pour gérer l’authentification, l’implémentation d’un service intégré ou personnalisé <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est traitée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="1c9ae-126">Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:blazor/security/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="1c9ae-127">Authentification du serveur</span><span class="sxs-lookup"><span data-stu-id="1c9ae-127"> Server authentication</span></span>

Blazor<span data-ttu-id="1c9ae-128">Les applications serveur fonctionnent sur une connexion en temps réel créée à l’aide de SignalR .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-128"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="1c9ae-129">L' [authentification dans les SignalR applications basées](xref:signalr/authn-and-authz) sur est gérée lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="1c9ae-130">L’authentification peut reposer sur un cookie ou un autre jeton du porteur.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="1c9ae-131">Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:blazor/security/server/index> .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="1c9ae-132">Service AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="1c9ae-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="1c9ae-133">Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service intégré obtient les données d’état d’authentification de l’ASP.net Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="1c9ae-134">C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET Core existants.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="1c9ae-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est le service sous-jacent utilisé par le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> et le composant <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> pour obtenir l’état d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="1c9ae-136">Vous n’utilisez généralement pas <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directement.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="1c9ae-137">Utilisez les approches [Composant AuthorizeView](#authorizeview-component) ou [Tâche\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-137">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="1c9ae-138">Le principal inconvénient de l’utilisation directe de <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="1c9ae-139">Le service <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> peut fournir les données <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur actuel, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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
            <li>@claim.Type: @claim.Value</li>
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

<span data-ttu-id="1c9ae-140">Si `user.Identity.IsAuthenticated` est `true` et parce que l’utilisateur est <xref:System.Security.Claims.ClaimsPrincipal>, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="1c9ae-141">Pour plus d’informations sur l’injection de dépendances et les services, consultez <xref:blazor/fundamentals/dependency-injection> et <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="1c9ae-142">Implémenter un AuthenticationStateProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="1c9ae-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="1c9ae-143">Si l’application requiert un fournisseur personnalisé, implémentez <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> et substituez `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="1c9ae-144">Dans une Blazor application Webassembly, le `CustomAuthStateProvider` service est inscrit `Main` dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1c9ae-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="1c9ae-145">Dans une Blazor application serveur, le `CustomAuthStateProvider` service est inscrit dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="1c9ae-146">À l’aide `CustomAuthStateProvider` de dans l’exemple précédent, tous les utilisateurs sont authentifiés avec le nom d’utilisateur `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="1c9ae-147">Exposer l’état d’authentification comme un paramètre en cascade</span><span class="sxs-lookup"><span data-stu-id="1c9ae-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="1c9ae-148">Si les données d’état d’authentification sont requises pour la logique procédurale, par exemple lors de l’exécution d’une action déclenchée par l’utilisateur, obtenez les données d’état d’authentification en définissant un paramètre en cascade de type `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

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

<span data-ttu-id="1c9ae-149">Si `user.Identity.IsAuthenticated` est `true`, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="1c9ae-150">Configurez le `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` paramètre en cascade à l’aide des <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> composants et dans le `App` composant (*app. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (*App.razor*):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="1c9ae-151">Dans une Blazor application Webassembly, ajoutez des services pour les options et l’autorisation pour `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="1c9ae-152">Dans une Blazor application serveur, les services pour les options et l’autorisation sont déjà présents, donc aucune action supplémentaire n’est requise.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="1c9ae-153">Autorisation</span><span class="sxs-lookup"><span data-stu-id="1c9ae-153">Authorization</span></span>

<span data-ttu-id="1c9ae-154">Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="1c9ae-155">L’accès est généralement accordé ou refusé selon les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="1c9ae-156">Un utilisateur est authentifié (connecté).</span><span class="sxs-lookup"><span data-stu-id="1c9ae-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="1c9ae-157">Un utilisateur appartient à un *rôle*.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-157">A user is in a *role*.</span></span>
* <span data-ttu-id="1c9ae-158">Un utilisateur a une *revendication*.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-158">A user has a *claim*.</span></span>
* <span data-ttu-id="1c9ae-159">Une *stratégie* est satisfaite.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="1c9ae-160">Chacun de ces concepts est identique à celui d’une application ASP.NET Core MVC ou Razor pages.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="1c9ae-161">Pour plus d’informations sur la sécurité de ASP.NET Core, consultez les articles sous [ASP.net Core sécurité et Identity ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="1c9ae-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="1c9ae-162">Composant AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="1c9ae-162">AuthorizeView component</span></span>

<span data-ttu-id="1c9ae-163">Le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> affiche sélectivement l’interface utilisateur en fonction de l’autorisation que l’utilisateur a pour l’afficher.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="1c9ae-164">Cette approche est utile lorsque vous devez uniquement *afficher* les données de l’utilisateur et que vous n’avez pas besoin d’utiliser l’identité de l’utilisateur dans la logique procédurale.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="1c9ae-165">Le composant expose une variable `context` de type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, que vous pouvez utiliser pour accéder aux informations relatives à l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="1c9ae-166">Vous pouvez également fournir un contenu différent à afficher si l’utilisateur n’est pas authentifié :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-166">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="1c9ae-167">Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> composant peut être utilisé dans le `NavMenu` composant (*Shared/NavMenu. Razor*) pour afficher un élément de liste ( `<li>...</li>` ) pour un [composant NavLink](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), mais notez que cette approche supprime uniquement l’élément de liste de la sortie rendue.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a [NavLink component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="1c9ae-168">Elle n’empêche pas l’utilisateur de naviguer jusqu’au composant.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="1c9ae-169">Le contenu des `<Authorized>` `<NotAuthorized>` balises et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="1c9ae-170">Les conditions d’autorisation, comme les rôles ou les stratégies qui contrôlent les options d’interface utilisateur ou d’accès, sont traitées dans la section [Autorisation](#authorization).</span><span class="sxs-lookup"><span data-stu-id="1c9ae-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="1c9ae-171">Si les conditions d’autorisation ne sont pas spécifiées, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> utilise une stratégie par défaut et traite :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="1c9ae-172">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="1c9ae-173">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="1c9ae-174">Autorisation en fonction du rôle et de la stratégie</span><span class="sxs-lookup"><span data-stu-id="1c9ae-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="1c9ae-175">Le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> prend en charge l’autorisation *basée sur le rôle* et *basée sur les stratégies*.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="1c9ae-176">Pour l’autorisation en fonction du rôle, utilisez le paramètre <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="1c9ae-177">Pour plus d’informations, consultez <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="1c9ae-178">Pour l’autorisation en fonction des stratégies, utilisez le paramètre <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="1c9ae-179">L’autorisation basée sur les revendications est un cas spécial d’autorisation basée sur les stratégies.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="1c9ae-180">Par exemple, vous pouvez définir une stratégie qui impose aux utilisateurs d’avoir une certaine revendication.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="1c9ae-181">Pour plus d’informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1c9ae-182">Ces API peuvent être utilisées dans les Blazor applications serveur ou Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="1c9ae-183">Si ni <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> ni <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> n’est spécifié, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> utilise la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="1c9ae-184">Contenu affiché lors de l’authentification asynchrone</span><span class="sxs-lookup"><span data-stu-id="1c9ae-184">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="1c9ae-185">permet de déterminer l’état d’authentification de *manière asynchrone*.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-185"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="1c9ae-186">Le scénario principal de cette approche se trouve dans les Blazor applications Webassembly qui effectuent une demande à un point de terminaison externe pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="1c9ae-187">Lorsque l’authentification est en cours, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> n’affiche aucun contenu par défaut.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="1c9ae-188">Pour afficher le contenu pendant que l’authentification se produit, utilisez l’élément `<Authorizing>` :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="1c9ae-189">Cette approche n’est normalement pas applicable aux Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-189">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="1c9ae-190">Les applications serveur connaissent l’état d’authentification dès que l’État est établi.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-190"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="1c9ae-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>le contenu peut être fourni dans Blazor le composant d’une application serveur <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , mais le contenu n’est jamais affiché.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="1c9ae-192">Attribut [Authorize]</span><span class="sxs-lookup"><span data-stu-id="1c9ae-192">[Authorize] attribute</span></span>

<span data-ttu-id="1c9ae-193">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut peut être utilisé dans les Razor composants :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="1c9ae-194">Utilisez uniquement [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sur `@page` les composants atteints via le Blazor routeur.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="1c9ae-195">L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="1c9ae-196">Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> à la place.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="1c9ae-197">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut prend également en charge l’autorisation basée sur les rôles ou la stratégie.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="1c9ae-198">Pour l’autorisation en fonction du rôle, utilisez le paramètre <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="1c9ae-199">Pour l’autorisation en fonction des stratégies, utilisez le paramètre <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="1c9ae-200">Si ni <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> ni <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> n’est spécifié, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) utilise la stratégie par défaut, qui par défaut consiste à traiter :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="1c9ae-201">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="1c9ae-202">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="1c9ae-203">Personnaliser le contenu non autorisé avec le composant Router</span><span class="sxs-lookup"><span data-stu-id="1c9ae-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="1c9ae-204">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, conjointement avec le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> composant, permet à l’application de spécifier du contenu personnalisé dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="1c9ae-205">Le contenu est introuvable.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-205">Content isn't found.</span></span>
* <span data-ttu-id="1c9ae-206">L’utilisateur n’a pas [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) de condition appliquée au composant.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="1c9ae-207">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est abordé dans la section [ `[Authorize]` attribut](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="1c9ae-208">Une authentification asynchrone est en cours.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="1c9ae-209">Dans le Blazor modèle de projet serveur par défaut, le `App` composant (*app. Razor*) montre comment définir un contenu personnalisé :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-209">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
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
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="1c9ae-210">Le contenu des `<NotFound>` `<NotAuthorized>` `<Authorizing>` balises, et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="1c9ae-211">Si l' `<NotAuthorized>` élément n’est pas spécifié, le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> utilise le message de secours suivant :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="1c9ae-212">Notification sur les changements d’état de l’authentification</span><span class="sxs-lookup"><span data-stu-id="1c9ae-212">Notification about authentication state changes</span></span>

<span data-ttu-id="1c9ae-213">Si l’application détermine que les données d’état d’authentification sous-jacentes ont changé (par exemple, parce que l’utilisateur s’est déconnecté ou qu’un autre utilisateur a modifié ses rôles), un [AuthenticationStateProvider personnalisé](#implement-a-custom-authenticationstateprovider) peut éventuellement appeler la méthode <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> sur la <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe de base.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="1c9ae-214">Cela prévient les consommateurs des données d’état d’authentification (par exemple, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="1c9ae-215">Logique procédurale</span><span class="sxs-lookup"><span data-stu-id="1c9ae-215">Procedural logic</span></span>

<span data-ttu-id="1c9ae-216">Si l’application est requise pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` pour obtenir le de l’utilisateur <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="1c9ae-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`peut être combiné avec d’autres services, tels que `IAuthorizationService` , pour évaluer des stratégies.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="1c9ae-218">Dans un Blazor composant d’application Webassembly, ajoutez les <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> espaces de noms et :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="1c9ae-219">Ces espaces de noms peuvent être fournis globalement en les ajoutant au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-219">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="1c9ae-220">Résoudre les erreurs</span><span class="sxs-lookup"><span data-stu-id="1c9ae-220">Troubleshoot errors</span></span>

<span data-ttu-id="1c9ae-221">Erreurs courantes :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-221">Common errors:</span></span>

* <span data-ttu-id="1c9ae-222">**L’autorisation nécessite un paramètre en cascade de type Task \<AuthenticationState> . Envisagez d’utiliser CascadingAuthenticationState pour fournir ce.**</span><span class="sxs-lookup"><span data-stu-id="1c9ae-222">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="1c9ae-223">**`null`la valeur est reçue pour`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="1c9ae-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="1c9ae-224">Il est probable que le projet n’a pas été créé à l’aide d’un Blazor modèle de serveur avec l’authentification activée.</span><span class="sxs-lookup"><span data-stu-id="1c9ae-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="1c9ae-225">Encapsulez un `<CascadingAuthenticationState>` autour d’une partie de l’arborescence de l’interface utilisateur, par exemple dans le `App` composant (*app. Razor*) comme suit :</span><span class="sxs-lookup"><span data-stu-id="1c9ae-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="1c9ae-226">Le <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> fournit le `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` paramètre en cascade, qui à son tour est reçu du service di sous-jacent <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="1c9ae-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c9ae-227">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c9ae-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="1c9ae-228">[Isard Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) exemples de liens vers la communauté d’authentification</span><span class="sxs-lookup"><span data-stu-id="1c9ae-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>