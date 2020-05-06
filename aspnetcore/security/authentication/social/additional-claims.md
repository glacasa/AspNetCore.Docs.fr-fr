---
title: Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des revendications et des jetons supplémentaires à partir de fournisseurs externes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769028"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="abf88-103">Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abf88-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abf88-104">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="abf88-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="abf88-105">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="abf88-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="abf88-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="abf88-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="abf88-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="abf88-107">Prerequisites</span></span>

<span data-ttu-id="abf88-108">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="abf88-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="abf88-109">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="abf88-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="abf88-110">Pour plus d’informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="abf88-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="abf88-111">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="abf88-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="abf88-112">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="abf88-112">Set the client ID and client secret</span></span>

<span data-ttu-id="abf88-113">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="abf88-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="abf88-114">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="abf88-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="abf88-115">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="abf88-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="abf88-116">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="abf88-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="abf88-117">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="abf88-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="abf88-118">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="abf88-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="abf88-119">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="abf88-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="abf88-120">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="abf88-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="abf88-121">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="abf88-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="abf88-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="abf88-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="abf88-123">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="abf88-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="abf88-124">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="abf88-124">Establish the authentication scope</span></span>

<span data-ttu-id="abf88-125">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>le.</span><span class="sxs-lookup"><span data-stu-id="abf88-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="abf88-126">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="abf88-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="abf88-127">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="abf88-127">Provider</span></span>  | <span data-ttu-id="abf88-128">Étendue</span><span class="sxs-lookup"><span data-stu-id="abf88-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="abf88-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="abf88-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="abf88-130">Google</span><span class="sxs-lookup"><span data-stu-id="abf88-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="abf88-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="abf88-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="abf88-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="abf88-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="abf88-133">Dans l’exemple d’application, la `userinfo.profile` portée de Google est automatiquement ajoutée par le <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> Framework quand est appelé <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>sur le.</span><span class="sxs-lookup"><span data-stu-id="abf88-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="abf88-134">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="abf88-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="abf88-135">Dans l’exemple suivant, l’étendue `https://www.googleapis.com/auth/user.birthday.read` Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="abf88-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="abf88-136">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="abf88-136">Map user data keys and create claims</span></span>

<span data-ttu-id="abf88-137">Dans les options du fournisseur, spécifiez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> ou pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="abf88-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="abf88-138">Pour plus d’informations sur les types de <xref:System.Security.Claims.ClaimTypes>revendication, consultez.</span><span class="sxs-lookup"><span data-stu-id="abf88-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="abf88-139">L’exemple d’application crée des revendications`urn:google:locale`de paramètres régionaux (`urn:google:picture`) et d’image `locale` ( `picture` ) à partir des clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="abf88-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="abf88-140">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="abf88-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="abf88-141">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker des `ApplicationUser` revendications pour les données utilisateur disponibles à <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>partir du.</span><span class="sxs-lookup"><span data-stu-id="abf88-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="abf88-142">Dans l’exemple d’application `OnPostConfirmationAsync` ,*(Account/ExternalLogin. cshtml. cs*) établit les revendications de`urn:google:locale`paramètres régionaux ()`urn:google:picture`et d’image () pour `ApplicationUser`le connecté, y compris <xref:System.Security.Claims.ClaimTypes.GivenName>une revendication pour :</span><span class="sxs-lookup"><span data-stu-id="abf88-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="abf88-143">Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="abf88-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="abf88-144">Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="abf88-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="abf88-145">Le navigateur détecte que l’en-tête du cookie est trop long.</span><span class="sxs-lookup"><span data-stu-id="abf88-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="abf88-146">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="abf88-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="abf88-147">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="abf88-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="abf88-148">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="abf88-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="abf88-149">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> authentification des cookies pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="abf88-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="abf88-150">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="abf88-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="abf88-151">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="abf88-151">Save the access token</span></span>

<span data-ttu-id="abf88-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dans le après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="abf88-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="abf88-153">`SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.</span><span class="sxs-lookup"><span data-stu-id="abf88-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="abf88-154">L’exemple d’application définit la valeur `SaveTokens` de `true` sur <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>dans :</span><span class="sxs-lookup"><span data-stu-id="abf88-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="abf88-155">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe `ApplicationUser`dans `AuthenticationProperties`le.</span><span class="sxs-lookup"><span data-stu-id="abf88-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="abf88-156">L’exemple d’application enregistre le jeton d' `OnPostConfirmationAsync` accès dans (nouvel enregistrement utilisateur `OnGetCallbackAsync` ) et (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="abf88-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="abf88-157">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="abf88-157">How to add additional custom tokens</span></span>

<span data-ttu-id="abf88-158">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre `SaveTokens`de, l’exemple d’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ajoute un avec <xref:System.DateTime> le actuel pour un `TicketCreated` [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de :</span><span class="sxs-lookup"><span data-stu-id="abf88-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="abf88-159">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="abf88-159">Creating and adding claims</span></span>

<span data-ttu-id="abf88-160">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="abf88-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="abf88-161">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="abf88-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="abf88-162">Les utilisateurs peuvent définir des actions personnalisées en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> dérivant de et en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> implémentant la méthode abstraite.</span><span class="sxs-lookup"><span data-stu-id="abf88-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="abf88-163">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="abf88-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="abf88-164">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="abf88-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="abf88-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> le donné de la collection.</span><span class="sxs-lookup"><span data-stu-id="abf88-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="abf88-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="abf88-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="abf88-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="abf88-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="abf88-168">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="abf88-168">Sample app output</span></span>

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

<span data-ttu-id="abf88-169">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="abf88-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="abf88-170">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="abf88-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="abf88-171">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="abf88-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="abf88-172">Prérequis</span><span class="sxs-lookup"><span data-stu-id="abf88-172">Prerequisites</span></span>

<span data-ttu-id="abf88-173">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="abf88-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="abf88-174">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="abf88-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="abf88-175">Pour plus d’informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="abf88-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="abf88-176">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="abf88-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="abf88-177">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="abf88-177">Set the client ID and client secret</span></span>

<span data-ttu-id="abf88-178">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="abf88-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="abf88-179">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="abf88-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="abf88-180">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="abf88-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="abf88-181">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="abf88-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="abf88-182">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="abf88-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="abf88-183">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="abf88-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="abf88-184">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="abf88-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="abf88-185">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="abf88-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="abf88-186">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="abf88-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="abf88-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="abf88-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="abf88-188">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="abf88-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="abf88-189">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="abf88-189">Establish the authentication scope</span></span>

<span data-ttu-id="abf88-190">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>le.</span><span class="sxs-lookup"><span data-stu-id="abf88-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="abf88-191">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="abf88-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="abf88-192">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="abf88-192">Provider</span></span>  | <span data-ttu-id="abf88-193">Étendue</span><span class="sxs-lookup"><span data-stu-id="abf88-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="abf88-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="abf88-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="abf88-195">Google</span><span class="sxs-lookup"><span data-stu-id="abf88-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="abf88-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="abf88-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="abf88-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="abf88-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="abf88-198">Dans l’exemple d’application, la `userinfo.profile` portée de Google est automatiquement ajoutée par le <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> Framework quand est appelé <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>sur le.</span><span class="sxs-lookup"><span data-stu-id="abf88-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="abf88-199">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="abf88-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="abf88-200">Dans l’exemple suivant, l’étendue `https://www.googleapis.com/auth/user.birthday.read` Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="abf88-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="abf88-201">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="abf88-201">Map user data keys and create claims</span></span>

<span data-ttu-id="abf88-202">Dans les options du fournisseur, spécifiez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> ou pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="abf88-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="abf88-203">Pour plus d’informations sur les types de <xref:System.Security.Claims.ClaimTypes>revendication, consultez.</span><span class="sxs-lookup"><span data-stu-id="abf88-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="abf88-204">L’exemple d’application crée des revendications`urn:google:locale`de paramètres régionaux (`urn:google:picture`) et d’image `locale` ( `picture` ) à partir des clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="abf88-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="abf88-205">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="abf88-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="abf88-206">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker des `ApplicationUser` revendications pour les données utilisateur disponibles à <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>partir du.</span><span class="sxs-lookup"><span data-stu-id="abf88-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="abf88-207">Dans l’exemple d’application `OnPostConfirmationAsync` ,*(Account/ExternalLogin. cshtml. cs*) établit les revendications de`urn:google:locale`paramètres régionaux ()`urn:google:picture`et d’image () pour `ApplicationUser`le connecté, y compris <xref:System.Security.Claims.ClaimTypes.GivenName>une revendication pour :</span><span class="sxs-lookup"><span data-stu-id="abf88-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="abf88-208">Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="abf88-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="abf88-209">Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="abf88-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="abf88-210">Le navigateur détecte que l’en-tête du cookie est trop long.</span><span class="sxs-lookup"><span data-stu-id="abf88-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="abf88-211">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="abf88-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="abf88-212">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="abf88-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="abf88-213">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="abf88-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="abf88-214">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> authentification des cookies pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="abf88-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="abf88-215">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="abf88-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="abf88-216">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="abf88-216">Save the access token</span></span>

<span data-ttu-id="abf88-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dans le après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="abf88-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="abf88-218">`SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.</span><span class="sxs-lookup"><span data-stu-id="abf88-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="abf88-219">L’exemple d’application définit la valeur `SaveTokens` de `true` sur <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>dans :</span><span class="sxs-lookup"><span data-stu-id="abf88-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="abf88-220">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe `ApplicationUser`dans `AuthenticationProperties`le.</span><span class="sxs-lookup"><span data-stu-id="abf88-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="abf88-221">L’exemple d’application enregistre le jeton d' `OnPostConfirmationAsync` accès dans (nouvel enregistrement utilisateur `OnGetCallbackAsync` ) et (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="abf88-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="abf88-222">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="abf88-222">How to add additional custom tokens</span></span>

<span data-ttu-id="abf88-223">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre `SaveTokens`de, l’exemple d’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ajoute un avec <xref:System.DateTime> le actuel pour un `TicketCreated` [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de :</span><span class="sxs-lookup"><span data-stu-id="abf88-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="abf88-224">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="abf88-224">Creating and adding claims</span></span>

<span data-ttu-id="abf88-225">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="abf88-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="abf88-226">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="abf88-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="abf88-227">Les utilisateurs peuvent définir des actions personnalisées en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> dérivant de et en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> implémentant la méthode abstraite.</span><span class="sxs-lookup"><span data-stu-id="abf88-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="abf88-228">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="abf88-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="abf88-229">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="abf88-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="abf88-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> le donné de la collection.</span><span class="sxs-lookup"><span data-stu-id="abf88-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="abf88-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="abf88-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="abf88-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="abf88-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="abf88-233">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="abf88-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="abf88-234">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="abf88-234">Additional resources</span></span>

* <span data-ttu-id="abf88-235">&ndash; [application SocialSample d’ingénierie dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) l’exemple d’application liée se trouve sur la branche d' `master` ingénierie [dotnet/AspNetCore GitHub référentiel](https://github.com/dotnet/AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="abf88-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="abf88-236">La `master` branche contient du code sous développement actif pour la prochaine version de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="abf88-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="abf88-237">Pour afficher une version de l’exemple d’application pour une version finale de ASP.NET Core, utilisez la liste déroulante **branche** pour sélectionner une branche de version `release/{X.Y}`(par exemple).</span><span class="sxs-lookup"><span data-stu-id="abf88-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
