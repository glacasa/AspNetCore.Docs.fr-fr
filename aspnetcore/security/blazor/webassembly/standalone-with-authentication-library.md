---
<span data-ttu-id="bea37-101">titre : «sécuriser une Blazor application autonome Webassembly ASP.net core avec la bibliothèque d’authentification auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="bea37-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bea37-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bea37-102">'Blazor'</span></span>
- <span data-ttu-id="bea37-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bea37-103">'Identity'</span></span>
- <span data-ttu-id="bea37-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bea37-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bea37-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bea37-105">'Razor'</span></span>
- <span data-ttu-id="bea37-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="bea37-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="bea37-107">Sécuriser une Blazor application autonome Webassembly ASP.net core à l’aide de la bibliothèque d’authentification</span><span class="sxs-lookup"><span data-stu-id="bea37-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="bea37-108">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bea37-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bea37-109">*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les instructions de cette rubrique. Consultez les rubriques AAD et AAD B2C dans ce nœud de table des matières.*</span><span class="sxs-lookup"><span data-stu-id="bea37-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="bea37-110">Pour créer une Blazor application Webassembly autonome qui utilise une `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="bea37-110">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="bea37-111">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="bea37-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bea37-112">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="bea37-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="bea37-113">Dans Visual Studio, [créez une Blazor application webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="bea37-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="bea37-114">Définissez **l’authentification** sur **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** .</span><span class="sxs-lookup"><span data-stu-id="bea37-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="bea37-115">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="bea37-115">Authentication package</span></span>

<span data-ttu-id="bea37-116">Quand une application est créée pour utiliser des comptes d’utilisateur individuels, l’application reçoit automatiquement une référence de package pour le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="bea37-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="bea37-117">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="bea37-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bea37-118">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="bea37-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="bea37-119">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="bea37-119">Authentication service support</span></span>

<span data-ttu-id="bea37-120">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la `AddOidcAuthentication` méthode d’extension fournie par le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Package.</span><span class="sxs-lookup"><span data-stu-id="bea37-120">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="bea37-121">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="bea37-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bea37-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bea37-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="bea37-123">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bea37-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="bea37-124">La prise en charge de l’authentification pour les applications autonomes est proposée à l’aide d’Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="bea37-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="bea37-125">La `AddOidcAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application à l’aide de OIDC.</span><span class="sxs-lookup"><span data-stu-id="bea37-125">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="bea37-126">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de l’adresse IP conforme à OIDC.</span><span class="sxs-lookup"><span data-stu-id="bea37-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="bea37-127">Obtenez les valeurs lors de l’inscription de l’application, qui se produit généralement dans son portail en ligne.</span><span class="sxs-lookup"><span data-stu-id="bea37-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="bea37-128">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="bea37-128">Access token scopes</span></span>

<span data-ttu-id="bea37-129">Le Blazor modèle Webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="bea37-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="bea37-130">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jetons par défaut du `OidcProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="bea37-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="bea37-131">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="bea37-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="bea37-132">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bea37-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bea37-133">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="bea37-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="bea37-134">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="bea37-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="bea37-135">Page d'index</span><span class="sxs-lookup"><span data-stu-id="bea37-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="bea37-136">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="bea37-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="bea37-137">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="bea37-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="bea37-138">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="bea37-138">LoginDisplay component</span></span>

<span data-ttu-id="bea37-139">Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans le `MainLayout` composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="bea37-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="bea37-140">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="bea37-140">For authenticated users:</span></span>
  * <span data-ttu-id="bea37-141">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="bea37-141">Displays the current username.</span></span>
  * <span data-ttu-id="bea37-142">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="bea37-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="bea37-143">Pour les utilisateurs anonymes, offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="bea37-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="bea37-144">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="bea37-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bea37-145">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bea37-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="bea37-146">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="bea37-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
