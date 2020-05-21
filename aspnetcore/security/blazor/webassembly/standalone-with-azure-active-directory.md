---
<span data-ttu-id="b1c98-101">titre : «sécuriser une Blazor application autonome Webassembly ASP.net core avec Azure Active Directory auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b1c98-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b1c98-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b1c98-102">'Blazor'</span></span>
- <span data-ttu-id="b1c98-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b1c98-103">'Identity'</span></span>
- <span data-ttu-id="b1c98-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b1c98-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b1c98-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b1c98-105">'Razor'</span></span>
- <span data-ttu-id="b1c98-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b1c98-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="b1c98-107">Sécuriser une Blazor application autonome Webassembly ASP.net core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b1c98-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="b1c98-108">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b1c98-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b1c98-109">Pour créer une Blazor application Webassembly autonome qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="b1c98-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="b1c98-110">[Créez un client et une application Web AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="b1c98-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="b1c98-111">Inscrire une application AAD dans la **Azure Active Directory**  >  zone de**inscriptions d’applications** Azure Active Directory de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="b1c98-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b1c98-112">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor AAD autonome\*\*).</span><span class="sxs-lookup"><span data-stu-id="b1c98-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="b1c98-113">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="b1c98-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="b1c98-114">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="b1c98-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="b1c98-115">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="b1c98-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b1c98-116">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="b1c98-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b1c98-117">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="b1c98-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b1c98-118">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="b1c98-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b1c98-119">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="b1c98-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b1c98-120">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="b1c98-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b1c98-121">Désactivez la case **Permissions**  >  à cocher autorisations**accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b1c98-121">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b1c98-122">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="b1c98-122">Select **Register**.</span></span>

<span data-ttu-id="b1c98-123">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1c98-123">Record the following information:</span></span>

* <span data-ttu-id="b1c98-124">ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="b1c98-124">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b1c98-125">ID de répertoire (ID de locataire) (par exemple, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="b1c98-125">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="b1c98-126">Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="b1c98-126">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b1c98-127">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="b1c98-127">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b1c98-128">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="b1c98-128">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b1c98-129">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="b1c98-129">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b1c98-130">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="b1c98-130">Select the **Save** button.</span></span>

<span data-ttu-id="b1c98-131">Créez l'application.</span><span class="sxs-lookup"><span data-stu-id="b1c98-131">Create the app.</span></span> <span data-ttu-id="b1c98-132">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="b1c98-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b1c98-133">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="b1c98-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b1c98-134">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="b1c98-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b1c98-135">Dans le portail Azure, l’URI de redirection Web de la plateforme **d’authentification**de l’application  >  **Platform configurations**  >  **Web**  >  **Redirect URI** est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="b1c98-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b1c98-136">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="b1c98-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b1c98-137">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="b1c98-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="b1c98-138">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1c98-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="b1c98-139">Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="b1c98-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="b1c98-140">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="b1c98-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="b1c98-141">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="b1c98-141">For more information, see:</span></span>
  * [<span data-ttu-id="b1c98-142">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="b1c98-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="b1c98-143">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="b1c98-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b1c98-144">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="b1c98-144">Authentication package</span></span>

<span data-ttu-id="b1c98-145">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="b1c98-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="b1c98-146">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="b1c98-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b1c98-147">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="b1c98-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b1c98-148">Le `Microsoft.Authentication.WebAssembly.Msal` Package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="b1c98-148">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b1c98-149">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="b1c98-149">Authentication service support</span></span>

<span data-ttu-id="b1c98-150">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` Package.</span><span class="sxs-lookup"><span data-stu-id="b1c98-150">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="b1c98-151">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="b1c98-151">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b1c98-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b1c98-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="b1c98-153">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="b1c98-153">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b1c98-154">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="b1c98-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="b1c98-155">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b1c98-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b1c98-156">Exemple :</span><span class="sxs-lookup"><span data-stu-id="b1c98-156">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="b1c98-157">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="b1c98-157">Access token scopes</span></span>

<span data-ttu-id="b1c98-158">Le Blazor modèle Webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="b1c98-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b1c98-159">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="b1c98-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b1c98-160">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="b1c98-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b1c98-161">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b1c98-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b1c98-162">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="b1c98-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b1c98-163">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="b1c98-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b1c98-164">Page d'index</span><span class="sxs-lookup"><span data-stu-id="b1c98-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b1c98-165">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="b1c98-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b1c98-166">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b1c98-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b1c98-167">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b1c98-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b1c98-168">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="b1c98-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b1c98-169">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b1c98-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="b1c98-170">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="b1c98-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="b1c98-171">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="b1c98-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
