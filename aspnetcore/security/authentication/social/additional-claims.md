---
<span data-ttu-id="1b256-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-102">'Blazor'</span></span>
- <span data-ttu-id="1b256-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-103">'Identity'</span></span>
- <span data-ttu-id="1b256-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-105">'Razor'</span></span>
- <span data-ttu-id="1b256-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="1b256-107">Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b256-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b256-108">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b256-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="1b256-109">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="1b256-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="1b256-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b256-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b256-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1b256-111">Prerequisites</span></span>

<span data-ttu-id="1b256-112">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1b256-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="1b256-113">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="1b256-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="1b256-114">Pour plus d'informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="1b256-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="1b256-115">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="1b256-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="1b256-116">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="1b256-116">Set the client ID and client secret</span></span>

<span data-ttu-id="1b256-117">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="1b256-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="1b256-118">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="1b256-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="1b256-119">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="1b256-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="1b256-120">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="1b256-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="1b256-121">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="1b256-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1b256-122">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="1b256-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="1b256-123">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="1b256-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1b256-124">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="1b256-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1b256-125">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="1b256-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="1b256-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="1b256-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="1b256-127">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="1b256-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="1b256-128">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="1b256-128">Establish the authentication scope</span></span>

<span data-ttu-id="1b256-129">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="1b256-130">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="1b256-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="1b256-131">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="1b256-131">Provider</span></span>  | <span data-ttu-id="1b256-132">Étendue</span><span class="sxs-lookup"><span data-stu-id="1b256-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="1b256-133">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-134">'Blazor'</span></span>
- <span data-ttu-id="1b256-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-135">'Identity'</span></span>
- <span data-ttu-id="1b256-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-137">'Razor'</span></span>
- <span data-ttu-id="1b256-138">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-139">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-140">'Blazor'</span></span>
- <span data-ttu-id="1b256-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-141">'Identity'</span></span>
- <span data-ttu-id="1b256-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-143">'Razor'</span></span>
- <span data-ttu-id="1b256-144">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-144">'SignalR' uid:</span></span> 

<span data-ttu-id="1b256-145">----- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-146">'Blazor'</span></span>
- <span data-ttu-id="1b256-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-147">'Identity'</span></span>
- <span data-ttu-id="1b256-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-149">'Razor'</span></span>
- <span data-ttu-id="1b256-150">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-151">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-152">'Blazor'</span></span>
- <span data-ttu-id="1b256-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-153">'Identity'</span></span>
- <span data-ttu-id="1b256-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-155">'Razor'</span></span>
- <span data-ttu-id="1b256-156">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-157">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-158">'Blazor'</span></span>
- <span data-ttu-id="1b256-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-159">'Identity'</span></span>
- <span data-ttu-id="1b256-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-161">'Razor'</span></span>
- <span data-ttu-id="1b256-162">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-163">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-164">'Blazor'</span></span>
- <span data-ttu-id="1b256-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-165">'Identity'</span></span>
- <span data-ttu-id="1b256-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-167">'Razor'</span></span>
- <span data-ttu-id="1b256-168">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-169">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-170">'Blazor'</span></span>
- <span data-ttu-id="1b256-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-171">'Identity'</span></span>
- <span data-ttu-id="1b256-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-173">'Razor'</span></span>
- <span data-ttu-id="1b256-174">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-175">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-176">'Blazor'</span></span>
- <span data-ttu-id="1b256-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-177">'Identity'</span></span>
- <span data-ttu-id="1b256-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-179">'Razor'</span></span>
- <span data-ttu-id="1b256-180">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-181">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-182">'Blazor'</span></span>
- <span data-ttu-id="1b256-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-183">'Identity'</span></span>
- <span data-ttu-id="1b256-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-185">'Razor'</span></span>
- <span data-ttu-id="1b256-186">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-187">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-188">'Blazor'</span></span>
- <span data-ttu-id="1b256-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-189">'Identity'</span></span>
- <span data-ttu-id="1b256-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-191">'Razor'</span></span>
- <span data-ttu-id="1b256-192">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-193">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-194">'Blazor'</span></span>
- <span data-ttu-id="1b256-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-195">'Identity'</span></span>
- <span data-ttu-id="1b256-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-197">'Razor'</span></span>
- <span data-ttu-id="1b256-198">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-199">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-200">'Blazor'</span></span>
- <span data-ttu-id="1b256-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-201">'Identity'</span></span>
- <span data-ttu-id="1b256-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-203">'Razor'</span></span>
- <span data-ttu-id="1b256-204">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-205">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-206">'Blazor'</span></span>
- <span data-ttu-id="1b256-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-207">'Identity'</span></span>
- <span data-ttu-id="1b256-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-209">'Razor'</span></span>
- <span data-ttu-id="1b256-210">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-211">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-212">'Blazor'</span></span>
- <span data-ttu-id="1b256-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-213">'Identity'</span></span>
- <span data-ttu-id="1b256-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-215">'Razor'</span></span>
- <span data-ttu-id="1b256-216">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-217">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-218">'Blazor'</span></span>
- <span data-ttu-id="1b256-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-219">'Identity'</span></span>
- <span data-ttu-id="1b256-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-221">'Razor'</span></span>
- <span data-ttu-id="1b256-222">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-223">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-224">'Blazor'</span></span>
- <span data-ttu-id="1b256-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-225">'Identity'</span></span>
- <span data-ttu-id="1b256-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-227">'Razor'</span></span>
- <span data-ttu-id="1b256-228">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-229">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-230">'Blazor'</span></span>
- <span data-ttu-id="1b256-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-231">'Identity'</span></span>
- <span data-ttu-id="1b256-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-233">'Razor'</span></span>
- <span data-ttu-id="1b256-234">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-235">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-236">'Blazor'</span></span>
- <span data-ttu-id="1b256-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-237">'Identity'</span></span>
- <span data-ttu-id="1b256-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-239">'Razor'</span></span>
- <span data-ttu-id="1b256-240">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-241">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-242">'Blazor'</span></span>
- <span data-ttu-id="1b256-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-243">'Identity'</span></span>
- <span data-ttu-id="1b256-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-245">'Razor'</span></span>
- <span data-ttu-id="1b256-246">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-247">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-248">'Blazor'</span></span>
- <span data-ttu-id="1b256-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-249">'Identity'</span></span>
- <span data-ttu-id="1b256-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-251">'Razor'</span></span>
- <span data-ttu-id="1b256-252">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-253">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-254">'Blazor'</span></span>
- <span data-ttu-id="1b256-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-255">'Identity'</span></span>
- <span data-ttu-id="1b256-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-257">'Razor'</span></span>
- <span data-ttu-id="1b256-258">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-259">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-260">'Blazor'</span></span>
- <span data-ttu-id="1b256-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-261">'Identity'</span></span>
- <span data-ttu-id="1b256-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-263">'Razor'</span></span>
- <span data-ttu-id="1b256-264">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-265">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-266">'Blazor'</span></span>
- <span data-ttu-id="1b256-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-267">'Identity'</span></span>
- <span data-ttu-id="1b256-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-269">'Razor'</span></span>
- <span data-ttu-id="1b256-270">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-271">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-272">'Blazor'</span></span>
- <span data-ttu-id="1b256-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-273">'Identity'</span></span>
- <span data-ttu-id="1b256-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-275">'Razor'</span></span>
- <span data-ttu-id="1b256-276">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-277">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-278">'Blazor'</span></span>
- <span data-ttu-id="1b256-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-279">'Identity'</span></span>
- <span data-ttu-id="1b256-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-281">'Razor'</span></span>
- <span data-ttu-id="1b256-282">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-283">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-284">'Blazor'</span></span>
- <span data-ttu-id="1b256-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-285">'Identity'</span></span>
- <span data-ttu-id="1b256-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-287">'Razor'</span></span>
- <span data-ttu-id="1b256-288">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-289">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-290">'Blazor'</span></span>
- <span data-ttu-id="1b256-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-291">'Identity'</span></span>
- <span data-ttu-id="1b256-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-293">'Razor'</span></span>
- <span data-ttu-id="1b256-294">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-295">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-296">'Blazor'</span></span>
- <span data-ttu-id="1b256-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-297">'Identity'</span></span>
- <span data-ttu-id="1b256-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-299">'Razor'</span></span>
- <span data-ttu-id="1b256-300">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-301">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-302">'Blazor'</span></span>
- <span data-ttu-id="1b256-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-303">'Identity'</span></span>
- <span data-ttu-id="1b256-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-305">'Razor'</span></span>
- <span data-ttu-id="1b256-306">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-307">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-308">'Blazor'</span></span>
- <span data-ttu-id="1b256-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-309">'Identity'</span></span>
- <span data-ttu-id="1b256-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-311">'Razor'</span></span>
- <span data-ttu-id="1b256-312">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-313">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-314">'Blazor'</span></span>
- <span data-ttu-id="1b256-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-315">'Identity'</span></span>
- <span data-ttu-id="1b256-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-317">'Razor'</span></span>
- <span data-ttu-id="1b256-318">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-319">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-320">'Blazor'</span></span>
- <span data-ttu-id="1b256-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-321">'Identity'</span></span>
- <span data-ttu-id="1b256-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-323">'Razor'</span></span>
- <span data-ttu-id="1b256-324">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-324">'SignalR' uid:</span></span> 

<span data-ttu-id="1b256-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="1b256-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="1b256-326">Dans l’exemple d’application, `userinfo.profile` la portée de Google est automatiquement ajoutée par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="1b256-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="1b256-327">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="1b256-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="1b256-328">Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1b256-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="1b256-329">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="1b256-329">Map user data keys and create claims</span></span>

<span data-ttu-id="1b256-330">Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="1b256-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="1b256-331">Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="1b256-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="1b256-332">L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="1b256-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="1b256-333">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="1b256-334">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="1b256-335">Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="1b256-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="1b256-336">Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1b256-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="1b256-337">Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1b256-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="1b256-338">Le navigateur détecte que l’en-tête du cookie est trop long.</span><span class="sxs-lookup"><span data-stu-id="1b256-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="1b256-339">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="1b256-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="1b256-340">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1b256-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="1b256-341">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="1b256-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="1b256-342">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d’authentification des cookies <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="1b256-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="1b256-343">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="1b256-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="1b256-344">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="1b256-344">Save the access token</span></span>

<span data-ttu-id="1b256-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="1b256-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="1b256-346">`SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.</span><span class="sxs-lookup"><span data-stu-id="1b256-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="1b256-347">L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="1b256-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="1b256-348">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="1b256-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="1b256-349">L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="1b256-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="1b256-350">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1b256-350">How to add additional custom tokens</span></span>

<span data-ttu-id="1b256-351">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="1b256-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="1b256-352">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="1b256-352">Creating and adding claims</span></span>

<span data-ttu-id="1b256-353">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="1b256-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="1b256-354">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="1b256-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="1b256-355">Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="1b256-356">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="1b256-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="1b256-357">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="1b256-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="1b256-358">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection.</span><span class="sxs-lookup"><span data-stu-id="1b256-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="1b256-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="1b256-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="1b256-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="1b256-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="1b256-361">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="1b256-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b256-362">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="1b256-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="1b256-363">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="1b256-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="1b256-364">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b256-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b256-365">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1b256-365">Prerequisites</span></span>

<span data-ttu-id="1b256-366">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1b256-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="1b256-367">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="1b256-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="1b256-368">Pour plus d'informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="1b256-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="1b256-369">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="1b256-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="1b256-370">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="1b256-370">Set the client ID and client secret</span></span>

<span data-ttu-id="1b256-371">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="1b256-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="1b256-372">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="1b256-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="1b256-373">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="1b256-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="1b256-374">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="1b256-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="1b256-375">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="1b256-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1b256-376">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="1b256-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="1b256-377">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="1b256-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1b256-378">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="1b256-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1b256-379">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="1b256-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="1b256-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="1b256-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="1b256-381">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="1b256-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="1b256-382">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="1b256-382">Establish the authentication scope</span></span>

<span data-ttu-id="1b256-383">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="1b256-384">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="1b256-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="1b256-385">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="1b256-385">Provider</span></span>  | <span data-ttu-id="1b256-386">Étendue</span><span class="sxs-lookup"><span data-stu-id="1b256-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="1b256-387">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-388">'Blazor'</span></span>
- <span data-ttu-id="1b256-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-389">'Identity'</span></span>
- <span data-ttu-id="1b256-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-391">'Razor'</span></span>
- <span data-ttu-id="1b256-392">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-393">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-394">'Blazor'</span></span>
- <span data-ttu-id="1b256-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-395">'Identity'</span></span>
- <span data-ttu-id="1b256-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-397">'Razor'</span></span>
- <span data-ttu-id="1b256-398">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-398">'SignalR' uid:</span></span> 

<span data-ttu-id="1b256-399">----- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-400">'Blazor'</span></span>
- <span data-ttu-id="1b256-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-401">'Identity'</span></span>
- <span data-ttu-id="1b256-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-403">'Razor'</span></span>
- <span data-ttu-id="1b256-404">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-405">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-406">'Blazor'</span></span>
- <span data-ttu-id="1b256-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-407">'Identity'</span></span>
- <span data-ttu-id="1b256-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-409">'Razor'</span></span>
- <span data-ttu-id="1b256-410">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-411">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-412">'Blazor'</span></span>
- <span data-ttu-id="1b256-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-413">'Identity'</span></span>
- <span data-ttu-id="1b256-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-415">'Razor'</span></span>
- <span data-ttu-id="1b256-416">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-417">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-418">'Blazor'</span></span>
- <span data-ttu-id="1b256-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-419">'Identity'</span></span>
- <span data-ttu-id="1b256-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-421">'Razor'</span></span>
- <span data-ttu-id="1b256-422">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-423">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-424">'Blazor'</span></span>
- <span data-ttu-id="1b256-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-425">'Identity'</span></span>
- <span data-ttu-id="1b256-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-427">'Razor'</span></span>
- <span data-ttu-id="1b256-428">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-429">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-430">'Blazor'</span></span>
- <span data-ttu-id="1b256-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-431">'Identity'</span></span>
- <span data-ttu-id="1b256-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-433">'Razor'</span></span>
- <span data-ttu-id="1b256-434">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-435">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-436">'Blazor'</span></span>
- <span data-ttu-id="1b256-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-437">'Identity'</span></span>
- <span data-ttu-id="1b256-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-439">'Razor'</span></span>
- <span data-ttu-id="1b256-440">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-441">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-442">'Blazor'</span></span>
- <span data-ttu-id="1b256-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-443">'Identity'</span></span>
- <span data-ttu-id="1b256-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-445">'Razor'</span></span>
- <span data-ttu-id="1b256-446">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-447">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-448">'Blazor'</span></span>
- <span data-ttu-id="1b256-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-449">'Identity'</span></span>
- <span data-ttu-id="1b256-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-451">'Razor'</span></span>
- <span data-ttu-id="1b256-452">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-453">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-454">'Blazor'</span></span>
- <span data-ttu-id="1b256-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-455">'Identity'</span></span>
- <span data-ttu-id="1b256-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-457">'Razor'</span></span>
- <span data-ttu-id="1b256-458">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-459">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-460">'Blazor'</span></span>
- <span data-ttu-id="1b256-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-461">'Identity'</span></span>
- <span data-ttu-id="1b256-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-463">'Razor'</span></span>
- <span data-ttu-id="1b256-464">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-465">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-466">'Blazor'</span></span>
- <span data-ttu-id="1b256-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-467">'Identity'</span></span>
- <span data-ttu-id="1b256-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-469">'Razor'</span></span>
- <span data-ttu-id="1b256-470">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-471">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-472">'Blazor'</span></span>
- <span data-ttu-id="1b256-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-473">'Identity'</span></span>
- <span data-ttu-id="1b256-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-475">'Razor'</span></span>
- <span data-ttu-id="1b256-476">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-477">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-478">'Blazor'</span></span>
- <span data-ttu-id="1b256-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-479">'Identity'</span></span>
- <span data-ttu-id="1b256-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-481">'Razor'</span></span>
- <span data-ttu-id="1b256-482">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-483">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-484">'Blazor'</span></span>
- <span data-ttu-id="1b256-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-485">'Identity'</span></span>
- <span data-ttu-id="1b256-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-487">'Razor'</span></span>
- <span data-ttu-id="1b256-488">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-489">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-490">'Blazor'</span></span>
- <span data-ttu-id="1b256-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-491">'Identity'</span></span>
- <span data-ttu-id="1b256-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-493">'Razor'</span></span>
- <span data-ttu-id="1b256-494">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-495">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-496">'Blazor'</span></span>
- <span data-ttu-id="1b256-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-497">'Identity'</span></span>
- <span data-ttu-id="1b256-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-499">'Razor'</span></span>
- <span data-ttu-id="1b256-500">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-501">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-502">'Blazor'</span></span>
- <span data-ttu-id="1b256-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-503">'Identity'</span></span>
- <span data-ttu-id="1b256-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-505">'Razor'</span></span>
- <span data-ttu-id="1b256-506">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-507">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-508">'Blazor'</span></span>
- <span data-ttu-id="1b256-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-509">'Identity'</span></span>
- <span data-ttu-id="1b256-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-511">'Razor'</span></span>
- <span data-ttu-id="1b256-512">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-513">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-514">'Blazor'</span></span>
- <span data-ttu-id="1b256-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-515">'Identity'</span></span>
- <span data-ttu-id="1b256-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-517">'Razor'</span></span>
- <span data-ttu-id="1b256-518">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-519">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-520">'Blazor'</span></span>
- <span data-ttu-id="1b256-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-521">'Identity'</span></span>
- <span data-ttu-id="1b256-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-523">'Razor'</span></span>
- <span data-ttu-id="1b256-524">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-525">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-526">'Blazor'</span></span>
- <span data-ttu-id="1b256-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-527">'Identity'</span></span>
- <span data-ttu-id="1b256-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-529">'Razor'</span></span>
- <span data-ttu-id="1b256-530">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-531">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-532">'Blazor'</span></span>
- <span data-ttu-id="1b256-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-533">'Identity'</span></span>
- <span data-ttu-id="1b256-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-535">'Razor'</span></span>
- <span data-ttu-id="1b256-536">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-537">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-538">'Blazor'</span></span>
- <span data-ttu-id="1b256-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-539">'Identity'</span></span>
- <span data-ttu-id="1b256-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-541">'Razor'</span></span>
- <span data-ttu-id="1b256-542">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-543">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-544">'Blazor'</span></span>
- <span data-ttu-id="1b256-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-545">'Identity'</span></span>
- <span data-ttu-id="1b256-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-547">'Razor'</span></span>
- <span data-ttu-id="1b256-548">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-549">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-550">'Blazor'</span></span>
- <span data-ttu-id="1b256-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-551">'Identity'</span></span>
- <span data-ttu-id="1b256-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-553">'Razor'</span></span>
- <span data-ttu-id="1b256-554">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-555">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-556">'Blazor'</span></span>
- <span data-ttu-id="1b256-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-557">'Identity'</span></span>
- <span data-ttu-id="1b256-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-559">'Razor'</span></span>
- <span data-ttu-id="1b256-560">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-561">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-562">'Blazor'</span></span>
- <span data-ttu-id="1b256-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-563">'Identity'</span></span>
- <span data-ttu-id="1b256-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-565">'Razor'</span></span>
- <span data-ttu-id="1b256-566">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-567">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-568">'Blazor'</span></span>
- <span data-ttu-id="1b256-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-569">'Identity'</span></span>
- <span data-ttu-id="1b256-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-571">'Razor'</span></span>
- <span data-ttu-id="1b256-572">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b256-573">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1b256-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b256-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b256-574">'Blazor'</span></span>
- <span data-ttu-id="1b256-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b256-575">'Identity'</span></span>
- <span data-ttu-id="1b256-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b256-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b256-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b256-577">'Razor'</span></span>
- <span data-ttu-id="1b256-578">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1b256-578">'SignalR' uid:</span></span> 

<span data-ttu-id="1b256-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="1b256-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="1b256-580">Dans l’exemple d’application, `userinfo.profile` la portée de Google est automatiquement ajoutée par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="1b256-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="1b256-581">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="1b256-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="1b256-582">Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1b256-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="1b256-583">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="1b256-583">Map user data keys and create claims</span></span>

<span data-ttu-id="1b256-584">Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="1b256-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="1b256-585">Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="1b256-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="1b256-586">L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="1b256-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="1b256-587">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="1b256-588">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="1b256-589">Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="1b256-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="1b256-590">Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1b256-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="1b256-591">Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1b256-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="1b256-592">Le navigateur détecte que l’en-tête du cookie est trop long.</span><span class="sxs-lookup"><span data-stu-id="1b256-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="1b256-593">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="1b256-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="1b256-594">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1b256-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="1b256-595">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="1b256-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="1b256-596">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d’authentification des cookies <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="1b256-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="1b256-597">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="1b256-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="1b256-598">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="1b256-598">Save the access token</span></span>

<span data-ttu-id="1b256-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="1b256-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="1b256-600">`SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.</span><span class="sxs-lookup"><span data-stu-id="1b256-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="1b256-601">L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="1b256-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="1b256-602">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="1b256-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="1b256-603">L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="1b256-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="1b256-604">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1b256-604">How to add additional custom tokens</span></span>

<span data-ttu-id="1b256-605">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="1b256-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="1b256-606">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="1b256-606">Creating and adding claims</span></span>

<span data-ttu-id="1b256-607">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="1b256-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="1b256-608">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="1b256-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="1b256-609">Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="1b256-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="1b256-610">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="1b256-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="1b256-611">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="1b256-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="1b256-612">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection.</span><span class="sxs-lookup"><span data-stu-id="1b256-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="1b256-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="1b256-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="1b256-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="1b256-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="1b256-615">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="1b256-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1b256-616">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1b256-616">Additional resources</span></span>

* <span data-ttu-id="1b256-617">[application SocialSample d’ingénierie dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): l’exemple d’application lié se trouve sur la branche [d’ingénierie dotnet/AspNetCore GitHub référentiel](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="1b256-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="1b256-618">La `master` branche contient du code sous développement actif pour la prochaine version de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="1b256-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="1b256-619">Pour afficher une version de l’exemple d’application pour une version finale de ASP.NET Core, utilisez la liste déroulante **branche** pour sélectionner une branche de version (par exemple `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="1b256-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
