---
title: Sécurisez Blazor les applications Core Server ASP.NET
author: guardrex
description: Découvrez comment atténuer les Blazor menaces de sécurité pour les applications Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626026"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="ea24a-103">Sécurisez les applications Core Blazor Server ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ea24a-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="ea24a-104">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ea24a-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="ea24a-105">Les applications Blazor Server adoptent un modèle de traitement de données *majestueux,* où le serveur et le client entretiennent une relation de longue durée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="ea24a-106">L’état persistant est maintenu par un [circuit,](xref:blazor/state-management)qui peut s’étendre sur des connexions qui sont également potentiellement à long terme.</span><span class="sxs-lookup"><span data-stu-id="ea24a-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="ea24a-107">Lorsqu’un utilisateur visite un site Blazor Server, le serveur crée un circuit dans la mémoire du serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="ea24a-108">Le circuit indique au navigateur quel contenu rendre et répond aux événements, comme lorsque l’utilisateur sélectionne un bouton dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="ea24a-109">Pour effectuer ces actions, un circuit invoque les fonctions JavaScript dans le navigateur de l’utilisateur et les méthodes .NET sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="ea24a-110">Cette interaction bidirectionnelle basée sur JavaScript est appelée [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ea24a-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="ea24a-111">Étant donné que JS interop se produit sur Internet et que le client utilise un navigateur distant, les applications Blazor Server partagent la plupart des problèmes de sécurité de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="ea24a-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="ea24a-112">Ce sujet décrit les menaces courantes pour les applications Blazor Server et fournit des conseils d’atténuation des menaces axés sur les applications orientées vers Internet.</span><span class="sxs-lookup"><span data-stu-id="ea24a-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="ea24a-113">Dans les environnements restreints, comme à l’intérieur des réseaux d’entreprise ou des intranets, certaines des directives d’atténuation sont les plus :</span><span class="sxs-lookup"><span data-stu-id="ea24a-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="ea24a-114">Ne s’applique pas dans l’environnement restreint.</span><span class="sxs-lookup"><span data-stu-id="ea24a-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="ea24a-115">Ne vaut pas le coût de mise en œuvre parce que le risque pour la sécurité est faible dans un environnement limité.</span><span class="sxs-lookup"><span data-stu-id="ea24a-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="ea24a-116">Modèle de projet Blazor Server</span><span class="sxs-lookup"><span data-stu-id="ea24a-116">Blazor Server project template</span></span>

<span data-ttu-id="ea24a-117">Le modèle de projet Blazor Server peut être configuré pour l’authentification lorsque le projet est créé.</span><span class="sxs-lookup"><span data-stu-id="ea24a-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea24a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea24a-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea24a-119">Suivez les conseils Visual <xref:blazor/get-started> Studio dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ea24a-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ea24a-120">Après avoir choisi le modèle **Application serveur Blazor** dans la boîte de dialogue **Créer une application web ASP.NET Core.**, sélectionnez **Modifier** sous **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="ea24a-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ea24a-121">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea24a-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ea24a-122">**Pas d’authentification**</span><span class="sxs-lookup"><span data-stu-id="ea24a-122">**No Authentication**</span></span>
* <span data-ttu-id="ea24a-123">**Comptes d’utilisateur individuels** &ndash; Les comptes d'utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="ea24a-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="ea24a-124">Dans l’application à l’aide du système [d’identité](xref:security/authentication/identity) d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea24a-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ea24a-125">Avec [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ea24a-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ea24a-126">**Comptes de travail ou d’école**</span><span class="sxs-lookup"><span data-stu-id="ea24a-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="ea24a-127">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="ea24a-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea24a-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea24a-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ea24a-129">Suivez les directives visual <xref:blazor/get-started> studio Code dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="ea24a-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ea24a-130">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ea24a-131">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ea24a-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="ea24a-132">Valeur `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="ea24a-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="ea24a-133">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ea24a-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="ea24a-134">Individuel</span><span class="sxs-lookup"><span data-stu-id="ea24a-134">Individual</span></span><br><span data-ttu-id="ea24a-135">Les utilisateurs stockés dans l’application avec l’identité ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea24a-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="ea24a-136">Individuel</span><span class="sxs-lookup"><span data-stu-id="ea24a-136">Individual</span></span><br><span data-ttu-id="ea24a-137">Les utilisateurs stockés dans [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ea24a-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="ea24a-138">Comptes professionnels ou scolaires</span><span class="sxs-lookup"><span data-stu-id="ea24a-138">Work or School Accounts</span></span><br><span data-ttu-id="ea24a-139">Authentification d’organisation pour un seul abonné.</span><span class="sxs-lookup"><span data-stu-id="ea24a-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="ea24a-140">Comptes professionnels ou scolaires</span><span class="sxs-lookup"><span data-stu-id="ea24a-140">Work or School Accounts</span></span><br><span data-ttu-id="ea24a-141">Authentification d’organisation pour plusieurs abonnés.</span><span class="sxs-lookup"><span data-stu-id="ea24a-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="ea24a-142">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ea24a-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="ea24a-143">La commande crée un dossier nommé avec la valeur fournie pour l’espace réservé `{APP NAME}` et utilise le nom de dossier en tant que nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="ea24a-144">Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea24a-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea24a-145">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ea24a-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ea24a-146">Suivez le Visual Studio pour <xref:blazor/get-started> Mac guide dans l’article.</span><span class="sxs-lookup"><span data-stu-id="ea24a-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="ea24a-147">Sur la configuration de votre nouvelle étape **d’application serveur Blazor,** sélectionnez **l’authentification individuelle (in-app)** de **l’authentification** tomber.</span><span class="sxs-lookup"><span data-stu-id="ea24a-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ea24a-148">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET’identité de base.</span><span class="sxs-lookup"><span data-stu-id="ea24a-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea24a-149">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea24a-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ea24a-150">Suivez les conseils CLI de <xref:blazor/get-started> base .NET dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="ea24a-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ea24a-151">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ea24a-152">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ea24a-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="ea24a-153">Valeur `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="ea24a-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="ea24a-154">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ea24a-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="ea24a-155">Individuel</span><span class="sxs-lookup"><span data-stu-id="ea24a-155">Individual</span></span><br><span data-ttu-id="ea24a-156">Les utilisateurs stockés dans l’application avec l’identité ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea24a-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="ea24a-157">Individuel</span><span class="sxs-lookup"><span data-stu-id="ea24a-157">Individual</span></span><br><span data-ttu-id="ea24a-158">Les utilisateurs stockés dans [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ea24a-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="ea24a-159">Comptes professionnels ou scolaires</span><span class="sxs-lookup"><span data-stu-id="ea24a-159">Work or School Accounts</span></span><br><span data-ttu-id="ea24a-160">Authentification d’organisation pour un seul abonné.</span><span class="sxs-lookup"><span data-stu-id="ea24a-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="ea24a-161">Comptes professionnels ou scolaires</span><span class="sxs-lookup"><span data-stu-id="ea24a-161">Work or School Accounts</span></span><br><span data-ttu-id="ea24a-162">Authentification d’organisation pour plusieurs abonnés.</span><span class="sxs-lookup"><span data-stu-id="ea24a-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="ea24a-163">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ea24a-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="ea24a-164">La commande crée un dossier nommé avec la valeur fournie pour l’espace réservé `{APP NAME}` et utilise le nom de dossier en tant que nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="ea24a-165">Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea24a-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="ea24a-166">Passez les jetons à une application Blazor Server</span><span class="sxs-lookup"><span data-stu-id="ea24a-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="ea24a-167">Authentifier l’application Blazor Server comme vous le feriez avec une application régulière Razor Pages ou MVC.</span><span class="sxs-lookup"><span data-stu-id="ea24a-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="ea24a-168">Fournir et enregistrer les jetons au cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ea24a-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="ea24a-169">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea24a-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="ea24a-170">Pour l’exemple de `Startup.ConfigureServices` code, y compris un exemple complet, voir les [jetons Passing à une application Blazor côté serveur](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="ea24a-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="ea24a-171">Définissez une classe à passer dans l’état initial de l’application avec les jetons d’accès et de rafraîchissement :</span><span class="sxs-lookup"><span data-stu-id="ea24a-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="ea24a-172">Définissez un service de fournisseur de jetons **à portée** qui peut être utilisé dans l’application Blazor pour résoudre les jetons de DI :</span><span class="sxs-lookup"><span data-stu-id="ea24a-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="ea24a-173">Dans `Startup.ConfigureServices`, ajouter des services pour:</span><span class="sxs-lookup"><span data-stu-id="ea24a-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="ea24a-174">Dans le fichier *_Host.cshtml,* créez et `InitialApplicationState` instancez et transmettez-le comme paramètre à l’application :</span><span class="sxs-lookup"><span data-stu-id="ea24a-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="ea24a-175">Dans `App` le composant (*App.razor*), résoudre le service et l’initialiser avec les données du paramètre :</span><span class="sxs-lookup"><span data-stu-id="ea24a-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="ea24a-176">Dans le service qui fait une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour appeler l’API :</span><span class="sxs-lookup"><span data-stu-id="ea24a-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="ea24a-177">Épuisement des ressources</span><span class="sxs-lookup"><span data-stu-id="ea24a-177">Resource exhaustion</span></span>

<span data-ttu-id="ea24a-178">L’épuisement des ressources peut se produire lorsqu’un client interagit avec le serveur et fait consommer des ressources excessives au serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="ea24a-179">La consommation excessive de ressources affecte principalement :</span><span class="sxs-lookup"><span data-stu-id="ea24a-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="ea24a-180">UC</span><span class="sxs-lookup"><span data-stu-id="ea24a-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="ea24a-181">Mémoire</span><span class="sxs-lookup"><span data-stu-id="ea24a-181">Memory</span></span>](#memory)
* [<span data-ttu-id="ea24a-182">Connexions clientes</span><span class="sxs-lookup"><span data-stu-id="ea24a-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="ea24a-183">Les attaques par déni de service (DoS) visent généralement à épuiser les ressources d’une application ou d’un serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="ea24a-184">Cependant, l’épuisement des ressources n’est pas nécessairement le résultat d’une attaque contre le système.</span><span class="sxs-lookup"><span data-stu-id="ea24a-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="ea24a-185">Par exemple, les ressources limitées peuvent être épuisées en raison de la forte demande des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="ea24a-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="ea24a-186">DoS est couvert plus loin dans la section [des attaques par déni de service (DoS).](#denial-of-service-dos-attacks)</span><span class="sxs-lookup"><span data-stu-id="ea24a-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="ea24a-187">Les ressources externes au cadre Blazor, telles que les bases de données et les poignées de fichiers (utilisées pour lire et écrire des fichiers), peuvent également faire l’expérience de l’épuisement des ressources.</span><span class="sxs-lookup"><span data-stu-id="ea24a-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="ea24a-188">Pour plus d’informations, consultez <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="ea24a-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="ea24a-189">UC</span><span class="sxs-lookup"><span data-stu-id="ea24a-189">CPU</span></span>

<span data-ttu-id="ea24a-190">L’épuisement du processeur peut se produire lorsqu’un ou plusieurs clients forcent le serveur à effectuer un travail intensif de processeur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="ea24a-191">Par exemple, considérez une application Blazor Server qui calcule un *numéro Fibonnacci*.</span><span class="sxs-lookup"><span data-stu-id="ea24a-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="ea24a-192">Un numéro Fibonnacci est produit à partir d’une séquence Fibonnacci, où chaque numéro de la séquence est la somme des deux nombres précédents.</span><span class="sxs-lookup"><span data-stu-id="ea24a-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="ea24a-193">La quantité de travail nécessaire pour atteindre la réponse dépend de la durée de la séquence et de la taille de la valeur initiale.</span><span class="sxs-lookup"><span data-stu-id="ea24a-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="ea24a-194">Si l’application ne limite pas la demande d’un client, les calculs à forte intensité de processeur peuvent dominer le temps du processeur et diminuer les performances d’autres tâches.</span><span class="sxs-lookup"><span data-stu-id="ea24a-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="ea24a-195">La consommation excessive de ressources est une préoccupation de sécurité qui a une incidence sur la disponibilité.</span><span class="sxs-lookup"><span data-stu-id="ea24a-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="ea24a-196">L’épuisement du processeur est une préoccupation pour toutes les applications orientées vers le public.</span><span class="sxs-lookup"><span data-stu-id="ea24a-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="ea24a-197">Dans les applications Web régulières, les demandes et les connexions s’affichent comme garantie, mais les applications Blazor Server ne fournissent pas les mêmes garanties.</span><span class="sxs-lookup"><span data-stu-id="ea24a-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="ea24a-198">Les applications Blazor Server doivent inclure des contrôles et des limites appropriés avant d’effectuer un travail potentiellement intensif au processeur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="ea24a-199">Mémoire</span><span class="sxs-lookup"><span data-stu-id="ea24a-199">Memory</span></span>

<span data-ttu-id="ea24a-200">L’épuisement de mémoire peut se produire quand un ou plusieurs clients forcent le serveur à consommer une grande quantité de mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea24a-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="ea24a-201">Par exemple, considérez une application côté Serveur Blazor avec un composant qui accepte et affiche une liste d’éléments.</span><span class="sxs-lookup"><span data-stu-id="ea24a-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="ea24a-202">Si l’application Blazor ne limite pas le nombre d’éléments autorisés ou le nombre d’éléments rendus au client, le traitement et le rendu à forte intensité de mémoire peuvent dominer la mémoire du serveur au point où les performances du serveur en souffrent.</span><span class="sxs-lookup"><span data-stu-id="ea24a-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="ea24a-203">Le serveur peut s’écraser ou ralentir au point qu’il semble s’être écrasé.</span><span class="sxs-lookup"><span data-stu-id="ea24a-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="ea24a-204">Considérez le scénario suivant pour maintenir et afficher une liste d’éléments qui se rapportent à un scénario potentiel d’épuisement de mémoire sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="ea24a-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="ea24a-205">Les éléments `List<MyItem>` d’une propriété ou d’un champ utilisent la mémoire du serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="ea24a-206">Si l’application permet à la liste des éléments de croître sans limites, il ya un risque de la panne du serveur à court de mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea24a-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="ea24a-207">Manquer de mémoire provoque la fin de la session actuelle (crash) et toutes les sessions simultanées dans cette instance serveur reçoivent une exception hors mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea24a-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="ea24a-208">Pour éviter que ce scénario ne se produise, l’application doit utiliser une structure de données qui impose une limite d’élément aux utilisateurs simultanés.</span><span class="sxs-lookup"><span data-stu-id="ea24a-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="ea24a-209">Si un système de mise à l’emploi n’est pas utilisé pour le rendu, le serveur utilise une mémoire supplémentaire pour les objets qui ne sont pas visibles dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="ea24a-210">Sans limite au nombre d’éléments, les exigences de mémoire peuvent épuiser la mémoire du serveur disponible.</span><span class="sxs-lookup"><span data-stu-id="ea24a-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="ea24a-211">Pour éviter ce scénario, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea24a-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="ea24a-212">Utilisez des listes paginées lors du rendu.</span><span class="sxs-lookup"><span data-stu-id="ea24a-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="ea24a-213">N’affichez que les 100 à 1 000 premiers éléments et obligez l’utilisateur à entrer des critères de recherche pour trouver des éléments au-delà des éléments affichés.</span><span class="sxs-lookup"><span data-stu-id="ea24a-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="ea24a-214">Pour un scénario de rendu plus avancé, implémentez des listes ou des grilles qui prennent en charge *la virtualisation.*</span><span class="sxs-lookup"><span data-stu-id="ea24a-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="ea24a-215">À l’aide de la virtualisation, les listes ne rendent qu’un sous-ensemble d’éléments actuellement visibles par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="ea24a-216">Lorsque l’utilisateur interagit avec la barre de défilement dans l’interface utilisateur, le composant ne rend que les éléments requis pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="ea24a-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="ea24a-217">Les articles qui ne sont pas actuellement nécessaires pour l’affichage peuvent être conservés dans le stockage secondaire, qui est l’approche idéale.</span><span class="sxs-lookup"><span data-stu-id="ea24a-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="ea24a-218">Les objets non joués peuvent également être conservés dans la mémoire, ce qui est moins idéal.</span><span class="sxs-lookup"><span data-stu-id="ea24a-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="ea24a-219">Les applications Blazor Server offrent un modèle de programmation similaire à d’autres cadres d’interface utilisateur pour les applications étatiques, telles que WPF, Windows Forms ou Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ea24a-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="ea24a-220">La principale différence est que dans plusieurs des cadres d’interface utilisateur la mémoire consommée par l’application appartient au client et affecte seulement ce client individuel.</span><span class="sxs-lookup"><span data-stu-id="ea24a-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="ea24a-221">Par exemple, une application WebAssembly Blazor s’exécute entièrement sur le client et n’utilise que les ressources de mémoire client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="ea24a-222">Dans le scénario Blazor Server, la mémoire consommée par l’application appartient au serveur et est partagée entre les clients sur l’instance serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="ea24a-223">Les exigences de mémoire côté serveur sont une considération pour toutes les applications Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ea24a-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="ea24a-224">Cependant, la plupart des applications Web sont apatrides, et la mémoire utilisée lors du traitement d’une demande est publiée lorsque la réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="ea24a-225">Comme recommandation générale, ne permettez pas aux clients d’allouer une quantité illimitée de mémoire comme dans toute autre application côté serveur qui persiste les connexions client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="ea24a-226">La mémoire consommée par une application Blazor Server persiste plus longtemps qu’une seule demande.</span><span class="sxs-lookup"><span data-stu-id="ea24a-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="ea24a-227">Pendant le développement, un profileur peut être utilisé ou une trace capturée pour évaluer les demandes de mémoire des clients.</span><span class="sxs-lookup"><span data-stu-id="ea24a-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="ea24a-228">Un profileur ou une trace ne capte pas la mémoire attribuée à un client spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea24a-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="ea24a-229">Pour capturer l’utilisation de mémoire d’un client spécifique pendant le développement, capturez un dépotoir et examinez la demande de mémoire de tous les objets enracinés sur le circuit d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="ea24a-230">Connexions clientes</span><span class="sxs-lookup"><span data-stu-id="ea24a-230">Client connections</span></span>

<span data-ttu-id="ea24a-231">L’épuisement de connexion peut se produire quand un ou plusieurs clients ouvrent trop de connexions simultanées au serveur, empêchant d’autres clients d’établir de nouvelles connexions.</span><span class="sxs-lookup"><span data-stu-id="ea24a-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="ea24a-232">Les clients de Blazor établissent une connexion unique par session et gardent la connexion ouverte aussi longtemps que la fenêtre du navigateur est ouverte.</span><span class="sxs-lookup"><span data-stu-id="ea24a-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="ea24a-233">Les exigences sur le serveur de maintenir toutes les connexions n’est pas spécifique aux applications Blazor.</span><span class="sxs-lookup"><span data-stu-id="ea24a-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="ea24a-234">Compte tenu de la nature persistante des connexions et de la nature acuité des applications Blazor Server, l’épuisement de connexion est un plus grand risque pour la disponibilité de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="ea24a-235">Par défaut, il n’y a pas de limite au nombre de connexions par utilisateur pour une application Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ea24a-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="ea24a-236">Si l’application nécessite une limite de connexion, prenez une ou plusieurs des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea24a-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="ea24a-237">Exiger l’authentification, ce qui limite naturellement la capacité des utilisateurs non autorisés à se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="ea24a-238">Pour que ce scénario soit efficace, les utilisateurs doivent être empêchés de fournir de nouveaux utilisateurs à volonté.</span><span class="sxs-lookup"><span data-stu-id="ea24a-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="ea24a-239">Limitez le nombre de connexions par utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-239">Limit the number of connections per user.</span></span> <span data-ttu-id="ea24a-240">Limiter les connexions peut être accomplie par les approches suivantes.</span><span class="sxs-lookup"><span data-stu-id="ea24a-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="ea24a-241">Faites preuve de prudence pour permettre aux utilisateurs légitimes d’accéder à l’application (par exemple, lorsqu’une limite de connexion est établie en fonction de l’adresse IP du client).</span><span class="sxs-lookup"><span data-stu-id="ea24a-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="ea24a-242">Au niveau de l’application :</span><span class="sxs-lookup"><span data-stu-id="ea24a-242">At the app level:</span></span>
    * <span data-ttu-id="ea24a-243">Extéabilité de routage de point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="ea24a-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="ea24a-244">Nécessitez une authentification pour se connecter à l’application et suivre les sessions actives par utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="ea24a-245">Rejeter les nouvelles sessions en atteignant une limite.</span><span class="sxs-lookup"><span data-stu-id="ea24a-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="ea24a-246">Proxy WebSocket connexions à une application grâce à l’utilisation d’un proxy, tels que le [service SignalR Azure](/azure/azure-signalr/signalr-overview) qui multiplexe les connexions des clients à une application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="ea24a-247">Cela fournit une application avec une plus grande capacité de connexion qu’un seul client peut établir, empêchant un client d’épuiser les connexions au serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="ea24a-248">Au niveau du serveur : Utilisez un proxy/passerelle en face de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="ea24a-249">Par exemple, [Azure Front Door](/azure/frontdoor/front-door-overview) vous permet de définir, gérer et surveiller le routage global du trafic Web vers une application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="ea24a-250">Attaques par déni de service (DoS)</span><span class="sxs-lookup"><span data-stu-id="ea24a-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="ea24a-251">Les attaques par déni de service (DoS) impliquent un client qui fait épuiser une ou plusieurs de ses ressources, ce qui rend l’application indisponible.</span><span class="sxs-lookup"><span data-stu-id="ea24a-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="ea24a-252">Les applications Blazor Server comprennent certaines limites par défaut et s’appuient sur d’autres limites ASP.NET Core et SignalR pour se protéger contre les attaques DoS :</span><span class="sxs-lookup"><span data-stu-id="ea24a-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="ea24a-253">Limite de l’application Blazor Server</span><span class="sxs-lookup"><span data-stu-id="ea24a-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="ea24a-254">Description</span><span class="sxs-lookup"><span data-stu-id="ea24a-254">Description</span></span> | <span data-ttu-id="ea24a-255">Default</span><span class="sxs-lookup"><span data-stu-id="ea24a-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="ea24a-256">Nombre maximum de circuits déconnectés qu’un serveur donné tient en mémoire à la fois.</span><span class="sxs-lookup"><span data-stu-id="ea24a-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="ea24a-257">100</span><span class="sxs-lookup"><span data-stu-id="ea24a-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="ea24a-258">La durée maximale d’un circuit déconnecté est conservée dans la mémoire avant d’être démolie.</span><span class="sxs-lookup"><span data-stu-id="ea24a-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="ea24a-259">3 minutes</span><span class="sxs-lookup"><span data-stu-id="ea24a-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="ea24a-260">Durée maximale du temps que le serveur attend avant de chronométrer une invocation asynchrone fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="ea24a-261">1 minute</span><span class="sxs-lookup"><span data-stu-id="ea24a-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="ea24a-262">Nombre maximum de lots de rendu non reconnus que le serveur garde en mémoire par circuit à un moment donné pour prendre en charge une reconnexion robuste.</span><span class="sxs-lookup"><span data-stu-id="ea24a-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="ea24a-263">Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots aient été reconnus par le client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="ea24a-264">10</span><span class="sxs-lookup"><span data-stu-id="ea24a-264">10</span></span> |


| <span data-ttu-id="ea24a-265">Limite de base signalR et ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ea24a-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="ea24a-266">Description</span><span class="sxs-lookup"><span data-stu-id="ea24a-266">Description</span></span> | <span data-ttu-id="ea24a-267">Default</span><span class="sxs-lookup"><span data-stu-id="ea24a-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="ea24a-268">Taille du message pour un message individuel.</span><span class="sxs-lookup"><span data-stu-id="ea24a-268">Message size for an individual message.</span></span> | <span data-ttu-id="ea24a-269">32 Ko</span><span class="sxs-lookup"><span data-stu-id="ea24a-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="ea24a-270">Interactions avec le navigateur (client)</span><span class="sxs-lookup"><span data-stu-id="ea24a-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="ea24a-271">Un client interagit avec le serveur par le biais de l’expédition de l’événement JS interop et rend l’achèvement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="ea24a-272">JS interop communication va dans les deux sens entre JavaScript et .NET:</span><span class="sxs-lookup"><span data-stu-id="ea24a-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="ea24a-273">Les événements du navigateur sont expédiés du client au serveur d’une manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="ea24a-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="ea24a-274">Le serveur répond asynchronement rerendering l’interface utilisateur si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ea24a-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="ea24a-275">Fonctions JavaScript invoquées à partir de .NET</span><span class="sxs-lookup"><span data-stu-id="ea24a-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="ea24a-276">Pour les appels à partir de méthodes .NET à JavaScript:</span><span class="sxs-lookup"><span data-stu-id="ea24a-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="ea24a-277">Toutes les invocations ont un délai d’attente <xref:System.OperationCanceledException> configurable après quoi ils échouent, retournant un à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="ea24a-278">Il ya un délai d’attente`CircuitOptions.JSInteropDefaultCallTimeout`par défaut pour les appels ( ) d’une minute.</span><span class="sxs-lookup"><span data-stu-id="ea24a-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="ea24a-279">Pour configurer cette <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limite, voir .</span><span class="sxs-lookup"><span data-stu-id="ea24a-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="ea24a-280">Un jeton d’annulation peut être fourni pour contrôler l’annulation par appel.</span><span class="sxs-lookup"><span data-stu-id="ea24a-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="ea24a-281">S’appuyer sur le délai d’attente d’appel par défaut dans la mesure du possible et en délai d’appel au client si un jeton d’annulation est fourni.</span><span class="sxs-lookup"><span data-stu-id="ea24a-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="ea24a-282">Le résultat d’un appel JavaScript ne peut pas être fiable.</span><span class="sxs-lookup"><span data-stu-id="ea24a-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="ea24a-283">Le Blazor client de l’application en cours d’exécution dans les recherches de navigateur pour la fonction JavaScript à invoquer.</span><span class="sxs-lookup"><span data-stu-id="ea24a-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="ea24a-284">La fonction est invoquée, et soit le résultat ou une erreur est produit.</span><span class="sxs-lookup"><span data-stu-id="ea24a-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="ea24a-285">Un client malveillant peut tenter de :</span><span class="sxs-lookup"><span data-stu-id="ea24a-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="ea24a-286">Causez un problème dans l’application en retournant une erreur de la fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="ea24a-287">Induire un comportement involontaire sur le serveur en retournant un résultat inattendu de la fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="ea24a-288">Prenez les précautions suivantes pour vous prémunir contre les scénarios précédents :</span><span class="sxs-lookup"><span data-stu-id="ea24a-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="ea24a-289">Enveloppez les appels interop JS dans les instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pour tenir compte des erreurs qui pourraient se produire pendant les invocations.</span><span class="sxs-lookup"><span data-stu-id="ea24a-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="ea24a-290">Pour plus d’informations, consultez <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="ea24a-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="ea24a-291">Valider les données retournées des invocations interop JS, y compris les messages d’erreur, avant de prendre des mesures.</span><span class="sxs-lookup"><span data-stu-id="ea24a-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="ea24a-292">.NET méthodes invoquées à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="ea24a-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="ea24a-293">Ne faites pas confiance aux appels de JavaScript vers des méthodes .NET.</span><span class="sxs-lookup"><span data-stu-id="ea24a-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="ea24a-294">Lorsqu’une méthode .NET est exposée à JavaScript, réfléchissez à la façon dont la méthode .NET est invoquée :</span><span class="sxs-lookup"><span data-stu-id="ea24a-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="ea24a-295">Traitez n’importe quelle méthode .NET exposée à JavaScript comme vous le feriez un point de terminaison public à l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="ea24a-296">Valider l’entrée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-296">Validate input.</span></span>
    * <span data-ttu-id="ea24a-297">Assurez-vous que les valeurs sont dans les fourchettes attendues.</span><span class="sxs-lookup"><span data-stu-id="ea24a-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="ea24a-298">Assurez-vous que l’utilisateur a la permission d’effectuer l’action demandée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="ea24a-299">N’allouez pas une quantité excessive de ressources dans le cadre de l’invocation de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="ea24a-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="ea24a-300">Par exemple, effectuez des contrôles et placez des limites sur le processeur et l’utilisation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea24a-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="ea24a-301">Prenez en compte que les méthodes statiques et d’instance peuvent être exposées aux clients JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="ea24a-302">Évitez de partager l’état entre les sessions à moins que la conception exige le partage de l’état avec des contraintes appropriées.</span><span class="sxs-lookup"><span data-stu-id="ea24a-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="ea24a-303">Par exemple, `DotNetReference` les méthodes exposées à travers des objets qui sont créés à l’origine par injection de dépendance (DI), les objets doivent être enregistrés comme des objets de portée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="ea24a-304">Cela s’applique à Blazor tout service DI que l’application Server utilise.</span><span class="sxs-lookup"><span data-stu-id="ea24a-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="ea24a-305">Pour les méthodes statiques, évitez d’établir un état qui ne peut être étendue au client à moins que l’application ne partage explicitement la conception par état de tous les utilisateurs sur une instance de serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="ea24a-306">Évitez de transmettre des données fournies par l’utilisateur dans les paramètres des appels JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="ea24a-307">Si l’adoption de données dans les paramètres est absolument nécessaire, assurez-vous que le code JavaScript gère le passage des données sans introduire les vulnérabilités [de script cross-site (XSS).](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="ea24a-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="ea24a-308">Par exemple, n’écrivez pas de données fournies par l’utilisateur au modèle d’objet document (DOM) en définissant la `innerHTML` propriété d’un élément.</span><span class="sxs-lookup"><span data-stu-id="ea24a-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="ea24a-309">Envisagez d’utiliser [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour désactiver `eval` et d’autres primitifs JavaScript dangereux.</span><span class="sxs-lookup"><span data-stu-id="ea24a-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="ea24a-310">Évitez d’implémenter l’envoi personnalisé d’invocations .NET en plus de la mise en œuvre de l’expédition du cadre.</span><span class="sxs-lookup"><span data-stu-id="ea24a-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="ea24a-311">Exposer les méthodes .NET au navigateur est un Blazor scénario avancé, pas recommandé pour le développement général.</span><span class="sxs-lookup"><span data-stu-id="ea24a-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="ea24a-312">Événements</span><span class="sxs-lookup"><span data-stu-id="ea24a-312">Events</span></span>

<span data-ttu-id="ea24a-313">Les événements fournissent un Blazor point d’entrée à une application Server.</span><span class="sxs-lookup"><span data-stu-id="ea24a-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="ea24a-314">Les mêmes règles pour la sauvegarde des paramètres dans Blazor les applications Web s’appliquent à la gestion d’événements dans les applications Server.</span><span class="sxs-lookup"><span data-stu-id="ea24a-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="ea24a-315">Un client malveillant peut envoyer toutes les données qu’il souhaite envoyer comme charge utile pour un événement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="ea24a-316">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea24a-316">For example:</span></span>

* <span data-ttu-id="ea24a-317">Un événement de `<select>` modification pour un pourrait envoyer une valeur qui n’est pas dans les options que l’application a présenté au client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="ea24a-318">Un `<input>` pourrait envoyer toutes les données de texte au serveur, en contournant la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="ea24a-319">L’application doit valider les données pour tout événement que l’application gère.</span><span class="sxs-lookup"><span data-stu-id="ea24a-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="ea24a-320">Les Blazor [formulaires-cadres](xref:blazor/forms-validation) effectuent des validations de base.</span><span class="sxs-lookup"><span data-stu-id="ea24a-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="ea24a-321">Si l’application utilise des composants de formulaires personnalisés, le code personnalisé doit être écrit pour valider les données de l’événement, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="ea24a-322">Les événements du serveur sont asynchrones, de sorte que plusieurs événements peuvent être envoyés sur le serveur avant que l’application a le temps de réagir en produisant un nouveau rendu.</span><span class="sxs-lookup"><span data-stu-id="ea24a-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="ea24a-323">Cela a des implications en matière de sécurité à prendre en considération.</span><span class="sxs-lookup"><span data-stu-id="ea24a-323">This has some security implications to consider.</span></span> <span data-ttu-id="ea24a-324">Limiter les actions des clients dans l’application doit être effectuée à l’intérieur des gestionnaires d’événements et ne pas dépendre de l’état de vue rendu en cours.</span><span class="sxs-lookup"><span data-stu-id="ea24a-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="ea24a-325">Considérez un composant de compteur qui devrait permettre à un utilisateur d’incrémenter un compteur un maximum de trois fois.</span><span class="sxs-lookup"><span data-stu-id="ea24a-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="ea24a-326">Le bouton pour incrémenter le compteur est `count`conditionnellement basé sur la valeur de :</span><span class="sxs-lookup"><span data-stu-id="ea24a-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="ea24a-327">Un client peut envoyer une ou plusieurs incréments avant que le cadre ne produise un nouveau rendu de ce composant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="ea24a-328">Le résultat est `count` que le peut être incrémenté *plus de trois fois* par l’utilisateur parce que le bouton n’est pas supprimé par l’interface utilisateur assez rapidement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="ea24a-329">La bonne façon d’atteindre `count` la limite de trois incréments est indiquée dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea24a-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="ea24a-330">En ajoutant `if (count < 3) { ... }` le chèque à l’intérieur `count` du gestionnaire, la décision d’incrément est basée sur l’état actuel de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="ea24a-331">La décision n’est pas fondée sur l’état de l’assurance-chômage comme elle l’était dans l’exemple précédent, qui pourrait être temporairement périmée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="ea24a-332">Se prémunir contre les expéditions multiples</span><span class="sxs-lookup"><span data-stu-id="ea24a-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="ea24a-333">Si un rappel d’événements invoque une opération de longue durée asynchrone, comme aller chercher des données à partir d’un service externe ou d’une base de données, envisagez d’utiliser un garde.</span><span class="sxs-lookup"><span data-stu-id="ea24a-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="ea24a-334">Le gardien peut empêcher l’utilisateur de faire la queue vers le haut de plusieurs opérations pendant que l’opération est en cours avec la rétroaction visuelle.</span><span class="sxs-lookup"><span data-stu-id="ea24a-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="ea24a-335">Le code composant `isLoading` `true` suivant `GetForecastAsync` s’établit à tout en obtient des données à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="ea24a-336">Alors `isLoading` `true`qu’il est, le bouton est désactivé dans l’interface utilisateur:</span><span class="sxs-lookup"><span data-stu-id="ea24a-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="ea24a-337">Le modèle de garde démontré dans l’exemple précédent fonctionne si l’opération de fond est exécutée asynchronement avec le `async` - `await` modèle.</span><span class="sxs-lookup"><span data-stu-id="ea24a-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="ea24a-338">Annuler tôt et éviter l’utilisation après-disposer</span><span class="sxs-lookup"><span data-stu-id="ea24a-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="ea24a-339">En plus d’utiliser un garde tel que décrit dans la [section De la Garde contre plusieurs dépêches,](#guard-against-multiple-dispatches) envisagez d’utiliser un <xref:System.Threading.CancellationToken> pour annuler les opérations de longue durée lorsque le composant est éliminé.</span><span class="sxs-lookup"><span data-stu-id="ea24a-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="ea24a-340">Cette approche a l’avantage supplémentaire d’éviter *l’utilisation après l’élimination* des composants :</span><span class="sxs-lookup"><span data-stu-id="ea24a-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="ea24a-341">Évitez les événements qui produisent de grandes quantités de données</span><span class="sxs-lookup"><span data-stu-id="ea24a-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="ea24a-342">Certains événements DOM, `oninput` `onscroll`tels que ou , peuvent produire une grande quantité de données.</span><span class="sxs-lookup"><span data-stu-id="ea24a-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="ea24a-343">Évitez d’utiliser Blazor ces événements dans les applications serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="ea24a-344">Orientations de sécurité supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ea24a-344">Additional security guidance</span></span>

<span data-ttu-id="ea24a-345">Les conseils pour sécuriser ASP.NET applications Blazor Core s’appliquent aux applications Server et sont couverts dans les sections suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea24a-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="ea24a-346">Enregistrement et données sensibles</span><span class="sxs-lookup"><span data-stu-id="ea24a-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="ea24a-347">Protéger les informations en transit avec HTTPS</span><span class="sxs-lookup"><span data-stu-id="ea24a-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="ea24a-348">[Script cross-site (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="ea24a-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="ea24a-349">Protection inter-origine</span><span class="sxs-lookup"><span data-stu-id="ea24a-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="ea24a-350">Cliquez-jacking</span><span class="sxs-lookup"><span data-stu-id="ea24a-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="ea24a-351">Ouvrez les redirections</span><span class="sxs-lookup"><span data-stu-id="ea24a-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="ea24a-352">Enregistrement et données sensibles</span><span class="sxs-lookup"><span data-stu-id="ea24a-352">Logging and sensitive data</span></span>

<span data-ttu-id="ea24a-353">Les interactions interop JS entre le client et le <xref:Microsoft.Extensions.Logging.ILogger> serveur sont enregistrées dans les journaux du serveur avec des instances.</span><span class="sxs-lookup"><span data-stu-id="ea24a-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="ea24a-354">évite l’enregistrement d’informations sensibles, telles que des événements réels ou des entrées et sorties interop JS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="ea24a-355">Lorsqu’une erreur se produit sur le serveur, le cadre informe le client et déchire la session.</span><span class="sxs-lookup"><span data-stu-id="ea24a-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="ea24a-356">Par défaut, le client reçoit un message d’erreur générique qui peut être vu dans les outils de développeur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="ea24a-357">L’erreur côté client n’inclut pas le site d’appels et ne fournit pas de détails sur la cause de l’erreur, mais les journaux du serveur contiennent de telles informations.</span><span class="sxs-lookup"><span data-stu-id="ea24a-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="ea24a-358">À des fins de développement, des informations sensibles sur les erreurs peuvent être mises à la disposition du client en permettant des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="ea24a-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="ea24a-359">Activez des erreurs détaillées avec :</span><span class="sxs-lookup"><span data-stu-id="ea24a-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="ea24a-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="ea24a-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="ea24a-361">`DetailedErrors`clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="ea24a-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="ea24a-362">Par exemple, `ASPNETCORE_DETAILEDERRORS` définissez la variable `true`de l’environnement à une valeur de .</span><span class="sxs-lookup"><span data-stu-id="ea24a-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ea24a-363">L’exposition d’informations d’erreur aux clients sur Internet est un risque pour la sécurité qui doit toujours être évité.</span><span class="sxs-lookup"><span data-stu-id="ea24a-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="ea24a-364">Protéger les informations en transit avec HTTPS</span><span class="sxs-lookup"><span data-stu-id="ea24a-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="ea24a-365">Utilisations SignalR du serveur pour la communication entre le client et le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="ea24a-366">Server utilise normalement le SignalR transport qui négocie, qui est généralement WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ea24a-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="ea24a-367">Server n’assure pas l’intégrité et la confidentialité des données envoyées entre le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="ea24a-368">Utilisez toujours HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="ea24a-369">Script cross-site (XSS)</span><span class="sxs-lookup"><span data-stu-id="ea24a-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="ea24a-370">Le script inter-sites (XSS) permet à une partie non autorisée d’exécuter une logique arbitraire dans le contexte du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="ea24a-371">Une application compromise pourrait potentiellement exécuter du code arbitraire sur le client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="ea24a-372">La vulnérabilité pourrait être utilisée pour effectuer potentiellement un certain nombre d’actions malveillantes contre le serveur:</span><span class="sxs-lookup"><span data-stu-id="ea24a-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="ea24a-373">Envoyer des événements faux/invalides au serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="ea24a-374">Répartition échouer / invalider rendre les achèvements.</span><span class="sxs-lookup"><span data-stu-id="ea24a-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="ea24a-375">Évitez d’envoyer des achèvements de rendu.</span><span class="sxs-lookup"><span data-stu-id="ea24a-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="ea24a-376">Envoi d’appels interop de JavaScript à .NET.</span><span class="sxs-lookup"><span data-stu-id="ea24a-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="ea24a-377">Modifier la réponse des appels interop de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea24a-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="ea24a-378">Évitez d’envoyer .NET aux résultats de JS interop.</span><span class="sxs-lookup"><span data-stu-id="ea24a-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="ea24a-379">Le Blazor cadre Server prend des mesures pour se protéger contre certaines des menaces précédentes :</span><span class="sxs-lookup"><span data-stu-id="ea24a-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="ea24a-380">Cesse de produire de nouvelles mises à jour de l’interface utilisateur si le client ne reconnaît pas les lots de rendu.</span><span class="sxs-lookup"><span data-stu-id="ea24a-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="ea24a-381">Configuré `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`avec .</span><span class="sxs-lookup"><span data-stu-id="ea24a-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="ea24a-382">Temps n’importe quel .NET à JavaScript appel après une minute sans recevoir une réponse du client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="ea24a-383">Configuré `CircuitOptions.JSInteropDefaultCallTimeout`avec .</span><span class="sxs-lookup"><span data-stu-id="ea24a-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="ea24a-384">Effectue la validation de base sur toutes les entrées provenant du navigateur pendant JS interop :</span><span class="sxs-lookup"><span data-stu-id="ea24a-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="ea24a-385">.Les références .NET sont valides et du type attendu par la méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="ea24a-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="ea24a-386">Les données ne sont pas mal formées.</span><span class="sxs-lookup"><span data-stu-id="ea24a-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="ea24a-387">Le nombre correct d’arguments pour la méthode sont présents dans la charge utile.</span><span class="sxs-lookup"><span data-stu-id="ea24a-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="ea24a-388">Les arguments ou le résultat peuvent être déséialisés correctement avant d’invoquer la méthode.</span><span class="sxs-lookup"><span data-stu-id="ea24a-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="ea24a-389">Effectue la validation de base dans toutes les entrées provenant du navigateur à partir d’événements expédiés :</span><span class="sxs-lookup"><span data-stu-id="ea24a-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="ea24a-390">L’événement a un type valide.</span><span class="sxs-lookup"><span data-stu-id="ea24a-390">The event has a valid type.</span></span>
  * <span data-ttu-id="ea24a-391">Les données de l’événement peuvent être déséialisées.</span><span class="sxs-lookup"><span data-stu-id="ea24a-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="ea24a-392">Il y a un gestionnaire d’événements associé à l’événement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="ea24a-393">En plus des garanties que le cadre met en œuvre, l’application doit être codée par le développeur pour se prémunir contre les menaces et prendre les mesures appropriées :</span><span class="sxs-lookup"><span data-stu-id="ea24a-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="ea24a-394">Validez toujours les données lors du traitement des événements.</span><span class="sxs-lookup"><span data-stu-id="ea24a-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="ea24a-395">Prenez les mesures appropriées dès la réception des données non valides :</span><span class="sxs-lookup"><span data-stu-id="ea24a-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="ea24a-396">Ignorer les données et le retour.</span><span class="sxs-lookup"><span data-stu-id="ea24a-396">Ignore the data and return.</span></span> <span data-ttu-id="ea24a-397">Cela permet à l’application de continuer à traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="ea24a-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="ea24a-398">Si l’application détermine que l’entrée est illégitime et ne pourrait pas être produite par un client légitime, jetez une exception.</span><span class="sxs-lookup"><span data-stu-id="ea24a-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="ea24a-399">Lancer une exception déchire le circuit et termine la séance.</span><span class="sxs-lookup"><span data-stu-id="ea24a-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="ea24a-400">Ne faites pas confiance au message d’erreur fourni par les achèvements de lots de rendu inclus dans les journaux.</span><span class="sxs-lookup"><span data-stu-id="ea24a-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="ea24a-401">L’erreur est fournie par le client et ne peut généralement pas faire confiance, car le client peut être compromis.</span><span class="sxs-lookup"><span data-stu-id="ea24a-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="ea24a-402">Ne faites pas confiance à l’entrée sur les appels interop JS dans les deux sens entre les méthodes JavaScript et .NET.</span><span class="sxs-lookup"><span data-stu-id="ea24a-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="ea24a-403">L’application est chargée de valider que le contenu des arguments et des résultats est valide, même si les arguments ou les résultats sont correctement déséialisés.</span><span class="sxs-lookup"><span data-stu-id="ea24a-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="ea24a-404">Pour qu’une vulnérabilité XSS existe, l’application doit intégrer l’entrée de l’utilisateur dans la page rendue.</span><span class="sxs-lookup"><span data-stu-id="ea24a-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="ea24a-405">Les composants du serveur exécutent une étape de compilation-temps où la majoration dans un fichier *.razor* est transformée en logique procédurale C.</span><span class="sxs-lookup"><span data-stu-id="ea24a-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="ea24a-406">Au moment de l’exécution, la logique Cmd construit un *arbre de rendu* décrivant les éléments, le texte et les composants de l’enfant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="ea24a-407">Ceci est appliqué au DOM du navigateur via une séquence d’instructions JavaScript (ou est sérialisé en HTML dans le cas de prerendering):</span><span class="sxs-lookup"><span data-stu-id="ea24a-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="ea24a-408">L’entrée de l’utilisateur rendue via `@someStringValue`la syntaxe Razor normale (par exemple, ) n’expose pas une vulnérabilité XSS parce que la syntaxe Razor est ajoutée au DOM via des commandes qui ne peuvent écrire que du texte.</span><span class="sxs-lookup"><span data-stu-id="ea24a-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="ea24a-409">Même si la valeur inclut la balisage HTML, la valeur est affichée sous forme de texte statique.</span><span class="sxs-lookup"><span data-stu-id="ea24a-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="ea24a-410">Lors du préréaidage, la sortie est codée HTML, qui affiche également le contenu sous forme de texte statique.</span><span class="sxs-lookup"><span data-stu-id="ea24a-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="ea24a-411">Les balises de script ne sont pas autorisées et ne devraient pas être incluses dans l’arbre de rendu des composants de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="ea24a-412">Si une balise de script est incluse dans le balisage d’un composant, une erreur de compilation est générée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="ea24a-413">Les auteurs de composants peuvent écrire des composants dans Cmd sans utiliser Razor.</span><span class="sxs-lookup"><span data-stu-id="ea24a-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="ea24a-414">L’auteur du composant est responsable de l’utilisation des API correctes lors de l’émission de sortie.</span><span class="sxs-lookup"><span data-stu-id="ea24a-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="ea24a-415">Par exemple, `builder.AddContent(0, someUserSuppliedString)` utiliser et *non,* `builder.AddMarkupContent(0, someUserSuppliedString)`car ce dernier pourrait créer une vulnérabilité XSS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="ea24a-416">Dans le cadre de la protection contre les attaques XSS, envisager la mise en œuvre des atténuations XSS, telles que [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span><span class="sxs-lookup"><span data-stu-id="ea24a-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="ea24a-417">Pour plus d’informations, consultez <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="ea24a-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="ea24a-418">Protection inter-origine</span><span class="sxs-lookup"><span data-stu-id="ea24a-418">Cross-origin protection</span></span>

<span data-ttu-id="ea24a-419">Les attaques d’origine croisée impliquent un client d’origine différente effectuant une action contre le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea24a-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="ea24a-420">L’action malveillante est généralement une demande GET ou un formulaire POST (Cross-Site Request Forgery, CSRF), mais l’ouverture d’un WebSocket malveillant est également possible.</span><span class="sxs-lookup"><span data-stu-id="ea24a-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="ea24a-421">Les applications serveur offrent [les SignalR mêmes garanties que toute autre application utilisant l’offre de protocole hub](xref:signalr/security):</span><span class="sxs-lookup"><span data-stu-id="ea24a-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="ea24a-422">Les applications serveur peuvent être consultées en sous-origine à moins que des mesures supplémentaires ne soient prises pour l’empêcher.</span><span class="sxs-lookup"><span data-stu-id="ea24a-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="ea24a-423">Pour désactiver l’accès à l’origine croisée, soit désactiver corS dans le point `DisableCorsAttribute` d’arrivée en ajoutant le middleware CORS au pipeline et en ajoutant les métadonnées au Blazor point de terminaison ou de limiter l’ensemble des origines autorisées en configurant [ SignalR pour le partage des ressources d’origine croisée](xref:signalr/security#cross-origin-resource-sharing).</span><span class="sxs-lookup"><span data-stu-id="ea24a-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="ea24a-424">Si corS est activé, des étapes supplémentaires pourraient être nécessaires pour protéger l’application en fonction de la configuration CORS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="ea24a-425">Si CORS est activé à l’échelle Blazor mondiale, CORS peut être désactivé pour le hub Server en ajoutant les `DisableCorsAttribute` métadonnées aux métadonnées de point de terminaison après avoir appelé `hub.MapBlazorHub()`.</span><span class="sxs-lookup"><span data-stu-id="ea24a-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="ea24a-426">Pour plus d’informations, consultez <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="ea24a-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="ea24a-427">Cliquez-jacking</span><span class="sxs-lookup"><span data-stu-id="ea24a-427">Click-jacking</span></span>

<span data-ttu-id="ea24a-428">Click-jacking consiste à rendre `<iframe>` un site à l’intérieur d’un site d’origine différente afin de tromper l’utilisateur en exécutant des actions sur le site attaqué.</span><span class="sxs-lookup"><span data-stu-id="ea24a-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="ea24a-429">Pour protéger une application contre `<iframe>`le rendu à l’intérieur d’une politique de [sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) et l’en-tête. `X-Frame-Options`</span><span class="sxs-lookup"><span data-stu-id="ea24a-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="ea24a-430">Pour plus d’informations, consultez [LES documents Web MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="ea24a-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="ea24a-431">Ouvrez les redirections</span><span class="sxs-lookup"><span data-stu-id="ea24a-431">Open redirects</span></span>

<span data-ttu-id="ea24a-432">Lorsque Blazor une session d’application Server commence, le serveur effectue la validation de base des URL envoyées dans le cadre du début de la session.</span><span class="sxs-lookup"><span data-stu-id="ea24a-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="ea24a-433">Le cadre vérifie que l’URL de base est un parent de l’URL actuelle avant d’établir le circuit.</span><span class="sxs-lookup"><span data-stu-id="ea24a-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="ea24a-434">Aucun contrôle supplémentaire n’est effectué par le cadre.</span><span class="sxs-lookup"><span data-stu-id="ea24a-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="ea24a-435">Lorsqu’un utilisateur sélectionne un lien sur le client, l’URL du lien est envoyée au serveur, ce qui détermine les mesures à prendre.</span><span class="sxs-lookup"><span data-stu-id="ea24a-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="ea24a-436">Par exemple, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’aller au nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="ea24a-437">Les composants peuvent également déclencher des demandes `NavigationManager`de navigation programatiquement grâce à l’utilisation de .</span><span class="sxs-lookup"><span data-stu-id="ea24a-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="ea24a-438">Dans de tels scénarios, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’aller au nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="ea24a-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="ea24a-439">Les composants doivent :</span><span class="sxs-lookup"><span data-stu-id="ea24a-439">Components must:</span></span>

* <span data-ttu-id="ea24a-440">Évitez d’utiliser l’entrée de l’utilisateur dans le cadre des arguments d’appel de navigation.</span><span class="sxs-lookup"><span data-stu-id="ea24a-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="ea24a-441">Validez les arguments pour s’assurer que la cible est autorisée par l’application.</span><span class="sxs-lookup"><span data-stu-id="ea24a-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="ea24a-442">Dans le cas contraire, un utilisateur malveillant peut forcer le navigateur à se rendre sur un site contrôlé par l’attaquant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="ea24a-443">Dans ce scénario, l’attaquant incite l’application à utiliser une partie `NavigationManager.Navigate` de l’entrée de l’utilisateur dans le cadre de l’invocation de la méthode.</span><span class="sxs-lookup"><span data-stu-id="ea24a-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="ea24a-444">Ce conseil s’applique également lors du rendu des liens dans le cadre de l’application :</span><span class="sxs-lookup"><span data-stu-id="ea24a-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="ea24a-445">Si possible, utilisez des liens relatifs.</span><span class="sxs-lookup"><span data-stu-id="ea24a-445">If possible, use relative links.</span></span>
* <span data-ttu-id="ea24a-446">Validez que les destinations de lien absolu sont valables avant de les inclure dans une page.</span><span class="sxs-lookup"><span data-stu-id="ea24a-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="ea24a-447">Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="ea24a-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="ea24a-448">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="ea24a-448">Authentication and authorization</span></span>

<span data-ttu-id="ea24a-449">Pour vous en indication sur <xref:security/blazor/index>l’authentification et l’autorisation, voir .</span><span class="sxs-lookup"><span data-stu-id="ea24a-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="ea24a-450">Liste de contrôle de sécurité</span><span class="sxs-lookup"><span data-stu-id="ea24a-450">Security checklist</span></span>

<span data-ttu-id="ea24a-451">La liste suivante des considérations de sécurité n’est pas exhaustive :</span><span class="sxs-lookup"><span data-stu-id="ea24a-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="ea24a-452">Validez les arguments des événements.</span><span class="sxs-lookup"><span data-stu-id="ea24a-452">Validate arguments from events.</span></span>
* <span data-ttu-id="ea24a-453">Validez les entrées et les résultats des appels interop JS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="ea24a-454">Évitez d’utiliser (ou de valider à l’avance) l’entrée de l’utilisateur pour les appels interop .NET à JS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="ea24a-455">Empêcher le client d’allouer une quantité de mémoire non liée.</span><span class="sxs-lookup"><span data-stu-id="ea24a-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="ea24a-456">Données dans le composant.</span><span class="sxs-lookup"><span data-stu-id="ea24a-456">Data within the component.</span></span>
  * <span data-ttu-id="ea24a-457">`DotNetObject`références retournées au client.</span><span class="sxs-lookup"><span data-stu-id="ea24a-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="ea24a-458">Garde contre plusieurs dépêches.</span><span class="sxs-lookup"><span data-stu-id="ea24a-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="ea24a-459">Annulez les opérations de longue durée lorsque le composant est éliminé.</span><span class="sxs-lookup"><span data-stu-id="ea24a-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="ea24a-460">Évitez les événements qui produisent de grandes quantités de données.</span><span class="sxs-lookup"><span data-stu-id="ea24a-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="ea24a-461">Évitez d’utiliser l’entrée `NavigationManager.Navigate` de l’utilisateur dans le cadre d’appels et de valider l’entrée de l’utilisateur pour les URL par rapport à un ensemble d’origines autorisées d’abord si inévitable.</span><span class="sxs-lookup"><span data-stu-id="ea24a-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="ea24a-462">Ne prenez pas de décisions d’autorisation en fonction de l’état de l’interface utilisateur, mais seulement de l’état de la composante.</span><span class="sxs-lookup"><span data-stu-id="ea24a-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="ea24a-463">Envisagez d’utiliser [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour vous protéger contre les attaques XSS.</span><span class="sxs-lookup"><span data-stu-id="ea24a-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="ea24a-464">Envisagez d’utiliser CSP et [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) pour vous protéger contre le clic-jacking.</span><span class="sxs-lookup"><span data-stu-id="ea24a-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="ea24a-465">Assurez-vous que les paramètres CORS sont appropriés Blazor lorsque vous activez CORS ou désactivez explicitement le CORS pour les applications.</span><span class="sxs-lookup"><span data-stu-id="ea24a-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="ea24a-466">Testez pour vous assurer que Blazor les limites côté serveur pour l’application offrent une expérience utilisateur acceptable sans niveaux de risque inacceptables.</span><span class="sxs-lookup"><span data-stu-id="ea24a-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
