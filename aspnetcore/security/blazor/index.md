---
<span data-ttu-id="96d78-101">titre : « ASP.NET Core Blazor l’authentification et l’autorisation » auteur : Description : « en savoir plus sur Blazor les scénarios d’authentification et d’autorisation ».</span><span class="sxs-lookup"><span data-stu-id="96d78-101">title: 'ASP.NET Core Blazor authentication and authorization' author: description: 'Learn about Blazor authentication and authorization scenarios.'</span></span>
<span data-ttu-id="96d78-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="96d78-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96d78-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96d78-103">'Blazor'</span></span>
- <span data-ttu-id="96d78-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96d78-104">'Identity'</span></span>
- <span data-ttu-id="96d78-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96d78-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="96d78-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96d78-106">'Razor'</span></span>
- <span data-ttu-id="96d78-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="96d78-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="96d78-108">BlazorAuthentification et autorisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="96d78-108">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="96d78-109">Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="96d78-109">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="96d78-110">ASP.NET Core prend en charge la configuration et la gestion de la sécurité dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="96d78-110">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="96d78-111">Les scénarios de sécurité diffèrent entre Blazor les applications serveur et Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="96d78-111">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="96d78-112">Étant donné que Blazor les applications serveur s’exécutent sur le serveur, les contrôles d’autorisation peuvent déterminer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="96d78-112">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="96d78-113">Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).</span><span class="sxs-lookup"><span data-stu-id="96d78-113">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="96d78-114">Les règles d’accès pour les zones de l’application et les composants.</span><span class="sxs-lookup"><span data-stu-id="96d78-114">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="96d78-115">Les applications webassembly s’exécutent sur le client.</span><span class="sxs-lookup"><span data-stu-id="96d78-115"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="96d78-116">L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher.</span><span class="sxs-lookup"><span data-stu-id="96d78-116">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="96d78-117">Étant donné que les contrôles côté client peuvent être modifiés ou ignorés par un utilisateur, une Blazor application Webassembly ne peut pas appliquer les règles d’accès d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="96d78-117">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="96d78-118">Les [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization) ne s’appliquent pas aux composants routables Razor .</span><span class="sxs-lookup"><span data-stu-id="96d78-118">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="96d78-119">Si un composant non routable Razor est [incorporé dans une page](xref:blazor/integrate-components#render-components-from-a-page-or-view), les conventions d’autorisation de la page affectent indirectement le Razor composant ainsi que le reste du contenu de la page.</span><span class="sxs-lookup"><span data-stu-id="96d78-119">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="96d78-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas pris en charge dans les Razor composants.</span><span class="sxs-lookup"><span data-stu-id="96d78-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="96d78-121">Authentification</span><span class="sxs-lookup"><span data-stu-id="96d78-121">Authentication</span></span>

Blazor<span data-ttu-id="96d78-122">utilise les mécanismes d’authentification ASP.NET Core existants pour établir l’identité de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="96d78-122"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="96d78-123">Le mécanisme exact dépend de la façon dont l' Blazor application est hébergée, Blazor webassembly ou Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="96d78-123">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="96d78-124">Authentification webassembly</span><span class="sxs-lookup"><span data-stu-id="96d78-124"> WebAssembly authentication</span></span>

<span data-ttu-id="96d78-125">Dans Blazor les applications Webassembly, les vérifications d’authentification peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="96d78-125">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="96d78-126">Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="96d78-126">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="96d78-127">Ajoutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="96d78-127">Add the following:</span></span>

* <span data-ttu-id="96d78-128">Référence de package pour [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) au fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="96d78-128">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="96d78-129">`Microsoft.AspNetCore.Components.Authorization`Espace de noms du fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="96d78-129">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="96d78-130">Pour gérer l’authentification, l’implémentation d’un service intégré ou personnalisé `AuthenticationStateProvider` est traitée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="96d78-130">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="96d78-131">Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:security/blazor/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="96d78-131">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="96d78-132">Authentification du serveur</span><span class="sxs-lookup"><span data-stu-id="96d78-132"> Server authentication</span></span>

Blazor<span data-ttu-id="96d78-133">Les applications serveur fonctionnent sur une connexion en temps réel créée à l’aide de SignalR .</span><span class="sxs-lookup"><span data-stu-id="96d78-133"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="96d78-134">L' [authentification dans les SignalR applications basées](xref:signalr/authn-and-authz) sur est gérée lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="96d78-134">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="96d78-135">L’authentification peut reposer sur un cookie ou un autre jeton du porteur.</span><span class="sxs-lookup"><span data-stu-id="96d78-135">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="96d78-136">Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:security/blazor/server/index> .</span><span class="sxs-lookup"><span data-stu-id="96d78-136">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="96d78-137">Service AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="96d78-137">AuthenticationStateProvider service</span></span>

<span data-ttu-id="96d78-138">Le `AuthenticationStateProvider` service intégré obtient les données d’état d’authentification de l’ASP.net Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="96d78-138">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="96d78-139">C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET Core existants.</span><span class="sxs-lookup"><span data-stu-id="96d78-139">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="96d78-140">`AuthenticationStateProvider` est le service sous-jacent utilisé par le composant `AuthorizeView` et le composant `CascadingAuthenticationState` pour obtenir l’état d’authentification.</span><span class="sxs-lookup"><span data-stu-id="96d78-140">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="96d78-141">Vous n’utilisez généralement pas `AuthenticationStateProvider` directement.</span><span class="sxs-lookup"><span data-stu-id="96d78-141">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="96d78-142">Utilisez le [composant AuthorizeView](#authorizeview-component) ou la [tâche \< AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approches décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="96d78-142">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="96d78-143">Le principal inconvénient de l’utilisation directe de `AuthenticationStateProvider` est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="96d78-143">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="96d78-144">Le service `AuthenticationStateProvider` peut fournir les données <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur actuel, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="96d78-144">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="96d78-145">Si `user.Identity.IsAuthenticated` est `true` et parce que l’utilisateur est <xref:System.Security.Claims.ClaimsPrincipal>, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="96d78-145">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="96d78-146">Pour plus d’informations sur l’injection de dépendances et les services, consultez <xref:blazor/dependency-injection> et <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="96d78-146">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="96d78-147">Implémenter un AuthenticationStateProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="96d78-147">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="96d78-148">Si l’application requiert un fournisseur personnalisé, implémentez `AuthenticationStateProvider` et substituez `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="96d78-148">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="96d78-149">Dans une Blazor application Webassembly, le `CustomAuthStateProvider` service est inscrit `Main` dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="96d78-149">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="96d78-150">Dans une Blazor application serveur, le `CustomAuthStateProvider` service est inscrit dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="96d78-150">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="96d78-151">À l’aide `CustomAuthStateProvider` de dans l’exemple précédent, tous les utilisateurs sont authentifiés avec le nom d’utilisateur `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="96d78-151">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="96d78-152">Exposer l’état d’authentification comme un paramètre en cascade</span><span class="sxs-lookup"><span data-stu-id="96d78-152">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="96d78-153">Si les données d’état d’authentification sont requises pour la logique procédurale, par exemple lors d’une action déclenchée par l’utilisateur, obtenez les données d’état d’authentification en définissant un paramètre en cascade de type `Task<AuthenticationState>` :</span><span class="sxs-lookup"><span data-stu-id="96d78-153">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="96d78-154">Si `user.Identity.IsAuthenticated` est `true`, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.</span><span class="sxs-lookup"><span data-stu-id="96d78-154">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="96d78-155">Configurez le `Task<AuthenticationState>` paramètre en cascade à l’aide des `AuthorizeRouteView` `CascadingAuthenticationState` composants et dans le `App` composant (*app. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="96d78-155">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="96d78-156">Dans une Blazor application Webassembly, ajoutez des services pour les options et l’autorisation pour `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="96d78-156">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="96d78-157">Dans une Blazor application serveur, les services pour les options et l’autorisation sont déjà présents, donc aucune action supplémentaire n’est requise.</span><span class="sxs-lookup"><span data-stu-id="96d78-157">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="96d78-158">Autorisation</span><span class="sxs-lookup"><span data-stu-id="96d78-158">Authorization</span></span>

<span data-ttu-id="96d78-159">Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.</span><span class="sxs-lookup"><span data-stu-id="96d78-159">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="96d78-160">L’accès est généralement accordé ou refusé selon les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="96d78-160">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="96d78-161">Un utilisateur est authentifié (connecté).</span><span class="sxs-lookup"><span data-stu-id="96d78-161">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="96d78-162">Un utilisateur appartient à un *rôle*.</span><span class="sxs-lookup"><span data-stu-id="96d78-162">A user is in a *role*.</span></span>
* <span data-ttu-id="96d78-163">Un utilisateur a une *revendication*.</span><span class="sxs-lookup"><span data-stu-id="96d78-163">A user has a *claim*.</span></span>
* <span data-ttu-id="96d78-164">Une *stratégie* est satisfaite.</span><span class="sxs-lookup"><span data-stu-id="96d78-164">A *policy* is satisfied.</span></span>

<span data-ttu-id="96d78-165">Chacun de ces concepts est identique à celui d’une application ASP.NET Core MVC ou Razor pages.</span><span class="sxs-lookup"><span data-stu-id="96d78-165">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="96d78-166">Pour plus d’informations sur la sécurité de ASP.NET Core, consultez les articles sous [ASP.net Core sécurité et Identity ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="96d78-166">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="96d78-167">Composant AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="96d78-167">AuthorizeView component</span></span>

<span data-ttu-id="96d78-168">Le composant `AuthorizeView` affiche sélectivement l’interface utilisateur en fonction de l’autorisation que l’utilisateur a pour l’afficher.</span><span class="sxs-lookup"><span data-stu-id="96d78-168">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="96d78-169">Cette approche est utile lorsque vous devez uniquement *afficher* les données de l’utilisateur et que vous n’avez pas besoin d’utiliser l’identité de l’utilisateur dans la logique procédurale.</span><span class="sxs-lookup"><span data-stu-id="96d78-169">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="96d78-170">Le composant expose une variable `context` de type `AuthenticationState`, que vous pouvez utiliser pour accéder aux informations relatives à l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="96d78-170">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="96d78-171">Vous pouvez également fournir un contenu différent à afficher si l’utilisateur n’est pas authentifié :</span><span class="sxs-lookup"><span data-stu-id="96d78-171">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="96d78-172">Le `AuthorizeView` composant peut être utilisé dans le `NavMenu` composant (*Shared/NavMenu. Razor*) pour afficher un élément de liste ( `<li>...</li>` ) pour un objet `NavLink` , mais notez que cette approche supprime uniquement l’élément de liste de la sortie rendue.</span><span class="sxs-lookup"><span data-stu-id="96d78-172">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="96d78-173">Elle n’empêche pas l’utilisateur de naviguer jusqu’au composant.</span><span class="sxs-lookup"><span data-stu-id="96d78-173">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="96d78-174">Le contenu des `<Authorized>` `<NotAuthorized>` balises et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="96d78-174">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="96d78-175">Les conditions d’autorisation, comme les rôles ou les stratégies qui contrôlent les options d’interface utilisateur ou d’accès, sont traitées dans la section [Autorisation](#authorization).</span><span class="sxs-lookup"><span data-stu-id="96d78-175">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="96d78-176">Si les conditions d’autorisation ne sont pas spécifiées, `AuthorizeView` utilise une stratégie par défaut et traite :</span><span class="sxs-lookup"><span data-stu-id="96d78-176">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="96d78-177">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="96d78-177">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="96d78-178">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="96d78-178">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="96d78-179">Autorisation en fonction du rôle et de la stratégie</span><span class="sxs-lookup"><span data-stu-id="96d78-179">Role-based and policy-based authorization</span></span>

<span data-ttu-id="96d78-180">Le composant `AuthorizeView` prend en charge l’autorisation *basée sur le rôle* et *basée sur les stratégies*.</span><span class="sxs-lookup"><span data-stu-id="96d78-180">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="96d78-181">Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :</span><span class="sxs-lookup"><span data-stu-id="96d78-181">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="96d78-182">Pour plus d'informations, consultez <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="96d78-182">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="96d78-183">Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :</span><span class="sxs-lookup"><span data-stu-id="96d78-183">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="96d78-184">L’autorisation basée sur les revendications est un cas spécial d’autorisation basée sur les stratégies.</span><span class="sxs-lookup"><span data-stu-id="96d78-184">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="96d78-185">Par exemple, vous pouvez définir une stratégie qui impose aux utilisateurs d’avoir une certaine revendication.</span><span class="sxs-lookup"><span data-stu-id="96d78-185">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="96d78-186">Pour plus d'informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="96d78-186">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="96d78-187">Ces API peuvent être utilisées dans les Blazor applications serveur ou Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="96d78-187">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="96d78-188">Si ni `Roles` ni `Policy` n’est spécifié, `AuthorizeView` utilise la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="96d78-188">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="96d78-189">Contenu affiché lors de l’authentification asynchrone</span><span class="sxs-lookup"><span data-stu-id="96d78-189">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="96d78-190">permet de déterminer l’état d’authentification de *manière asynchrone*.</span><span class="sxs-lookup"><span data-stu-id="96d78-190"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="96d78-191">Le scénario principal de cette approche se trouve dans les Blazor applications Webassembly qui effectuent une demande à un point de terminaison externe pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="96d78-191">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="96d78-192">Lorsque l’authentification est en cours, `AuthorizeView` n’affiche aucun contenu par défaut.</span><span class="sxs-lookup"><span data-stu-id="96d78-192">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="96d78-193">Pour afficher le contenu pendant que l’authentification se produit, utilisez l’élément `<Authorizing>` :</span><span class="sxs-lookup"><span data-stu-id="96d78-193">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="96d78-194">Cette approche n’est normalement pas applicable aux Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="96d78-194">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="96d78-195">Les applications serveur connaissent l’état d’authentification dès que l’État est établi.</span><span class="sxs-lookup"><span data-stu-id="96d78-195"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="96d78-196">`Authorizing`le contenu peut être fourni dans Blazor le composant d’une application serveur `AuthorizeView` , mais le contenu n’est jamais affiché.</span><span class="sxs-lookup"><span data-stu-id="96d78-196">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="96d78-197">Attribut [Authorize]</span><span class="sxs-lookup"><span data-stu-id="96d78-197">[Authorize] attribute</span></span>

<span data-ttu-id="96d78-198">L' `[Authorize]` attribut peut être utilisé dans les Razor composants :</span><span class="sxs-lookup"><span data-stu-id="96d78-198">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="96d78-199">Utilisez uniquement `[Authorize]` sur `@page` les composants atteints via le Blazor routeur.</span><span class="sxs-lookup"><span data-stu-id="96d78-199">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="96d78-200">L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page.</span><span class="sxs-lookup"><span data-stu-id="96d78-200">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="96d78-201">Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez `AuthorizeView` à la place.</span><span class="sxs-lookup"><span data-stu-id="96d78-201">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="96d78-202">L’attribut `[Authorize]` prend également en charge l’autorisation en fonction du rôle ou des stratégies.</span><span class="sxs-lookup"><span data-stu-id="96d78-202">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="96d78-203">Pour l’autorisation en fonction du rôle, utilisez le paramètre `Roles` :</span><span class="sxs-lookup"><span data-stu-id="96d78-203">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="96d78-204">Pour l’autorisation en fonction des stratégies, utilisez le paramètre `Policy` :</span><span class="sxs-lookup"><span data-stu-id="96d78-204">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="96d78-205">Si ni `Roles` ni `Policy` n’est spécifié, `[Authorize]` utilise la stratégie par défaut, qui consiste par défaut à traiter :</span><span class="sxs-lookup"><span data-stu-id="96d78-205">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="96d78-206">Les utilisateurs authentifiés (connectés) comme étant autorisés.</span><span class="sxs-lookup"><span data-stu-id="96d78-206">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="96d78-207">Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.</span><span class="sxs-lookup"><span data-stu-id="96d78-207">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="96d78-208">Personnaliser le contenu non autorisé avec le composant Router</span><span class="sxs-lookup"><span data-stu-id="96d78-208">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="96d78-209">Le `Router` composant, conjointement avec le `AuthorizeRouteView` composant, permet à l’application de spécifier du contenu personnalisé dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="96d78-209">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="96d78-210">Le contenu est introuvable.</span><span class="sxs-lookup"><span data-stu-id="96d78-210">Content isn't found.</span></span>
* <span data-ttu-id="96d78-211">L’utilisateur ne répond pas à une condition `[Authorize]` appliquée au composant.</span><span class="sxs-lookup"><span data-stu-id="96d78-211">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="96d78-212">L' `[Authorize]` attribut est abordé dans la section [ `[Authorize]` attribut](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="96d78-212">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="96d78-213">Une authentification asynchrone est en cours.</span><span class="sxs-lookup"><span data-stu-id="96d78-213">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="96d78-214">Dans le Blazor modèle de projet serveur par défaut, le `App` composant (*app. Razor*) montre comment définir un contenu personnalisé :</span><span class="sxs-lookup"><span data-stu-id="96d78-214">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="96d78-215">Le contenu des `<NotFound>` `<NotAuthorized>` `<Authorizing>` balises, et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="96d78-215">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="96d78-216">Si l' `<NotAuthorized>` élément n’est pas spécifié, le `AuthorizeRouteView` utilise le message de secours suivant :</span><span class="sxs-lookup"><span data-stu-id="96d78-216">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="96d78-217">Notification sur les changements d’état de l’authentification</span><span class="sxs-lookup"><span data-stu-id="96d78-217">Notification about authentication state changes</span></span>

<span data-ttu-id="96d78-218">Si l’application détermine que les données d’état d’authentification sous-jacentes ont changé (par exemple, parce que l’utilisateur s’est déconnecté ou qu’un autre utilisateur a modifié ses rôles), un [AuthenticationStateProvider personnalisé](#implement-a-custom-authenticationstateprovider) peut éventuellement appeler la méthode `NotifyAuthenticationStateChanged` sur la `AuthenticationStateProvider` classe de base.</span><span class="sxs-lookup"><span data-stu-id="96d78-218">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="96d78-219">Cela prévient les consommateurs des données d’état d’authentification (par exemple, `AuthorizeView`) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.</span><span class="sxs-lookup"><span data-stu-id="96d78-219">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="96d78-220">Logique procédurale</span><span class="sxs-lookup"><span data-stu-id="96d78-220">Procedural logic</span></span>

<span data-ttu-id="96d78-221">Si l’application est nécessaire pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<AuthenticationState>` pour obtenir le <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="96d78-221">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="96d78-222">`Task<AuthenticationState>` peut être combiné avec d’autres services, comme `IAuthorizationService`, pour évaluer les stratégies.</span><span class="sxs-lookup"><span data-stu-id="96d78-222">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="96d78-223">Dans un Blazor composant d’application Webassembly, ajoutez les `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` espaces de noms et :</span><span class="sxs-lookup"><span data-stu-id="96d78-223">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="96d78-224">Ces espaces de noms peuvent être fournis globalement en les ajoutant au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="96d78-224">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="96d78-225">Autorisation dans les Blazor applications Webassembly</span><span class="sxs-lookup"><span data-stu-id="96d78-225">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="96d78-226">Dans Blazor les applications Webassembly, les vérifications d’autorisation peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="96d78-226">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="96d78-227">Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="96d78-227">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="96d78-228">**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**</span><span class="sxs-lookup"><span data-stu-id="96d78-228">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="96d78-229">Pour plus d’informations, consultez les articles sous <xref:security/blazor/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="96d78-229">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="96d78-230">Résoudre les erreurs</span><span class="sxs-lookup"><span data-stu-id="96d78-230">Troubleshoot errors</span></span>

<span data-ttu-id="96d78-231">Erreurs courantes :</span><span class="sxs-lookup"><span data-stu-id="96d78-231">Common errors:</span></span>

* <span data-ttu-id="96d78-232">**L’autorisation nécessite un paramètre en cascade de type Task \< AuthenticationState>. Envisagez d’utiliser CascadingAuthenticationState pour fournir ce.**</span><span class="sxs-lookup"><span data-stu-id="96d78-232">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="96d78-233">**`null`la valeur est reçue pour`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="96d78-233">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="96d78-234">Il est probable que le projet n’a pas été créé à l’aide d’un Blazor modèle de serveur avec l’authentification activée.</span><span class="sxs-lookup"><span data-stu-id="96d78-234">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="96d78-235">Encapsulez un `<CascadingAuthenticationState>` autour d’une partie de l’arborescence de l’interface utilisateur, par exemple dans le `App` composant (*app. Razor*) comme suit :</span><span class="sxs-lookup"><span data-stu-id="96d78-235">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="96d78-236">`CascadingAuthenticationState` fournit le paramètre en cascade `Task<AuthenticationState>`, qu’il reçoit à son tour du service d’injection de dépendances `AuthenticationStateProvider` sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="96d78-236">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96d78-237">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="96d78-237">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="96d78-238">[Isard Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) exemples de liens vers la communauté d’authentification</span><span class="sxs-lookup"><span data-stu-id="96d78-238">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
