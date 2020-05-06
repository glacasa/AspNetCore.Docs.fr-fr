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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter. Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application. Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client. Pour plus d’informations, consultez <xref:security/authentication/social/index>. L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Définir l’ID client et la clé secrète client

Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client. Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur. Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur. Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Google](xref:security/authentication/google-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs d’authentification](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Établir l’étendue de l’authentification

Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>le. Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.

| Fournisseur  | Étendue                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Dans l’exemple d’application, la `userinfo.profile` portée de Google est automatiquement ajoutée par le <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> Framework quand est appelé <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>sur le. Si l’application requiert des étendues supplémentaires, ajoutez-les aux options. Dans l’exemple suivant, l’étendue `https://www.googleapis.com/auth/user.birthday.read` Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapper des clés de données utilisateur et créer des revendications

Dans les options du fournisseur, spécifiez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> ou pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion. Pour plus d’informations sur les types de <xref:System.Security.Claims.ClaimTypes>revendication, consultez.

L’exemple d’application crée des revendications`urn:google:locale`de paramètres régionaux (`urn:google:picture`) et d’image `locale` ( `picture` ) à partir des clés et dans les données utilisateur de Google :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker des `ApplicationUser` revendications pour les données utilisateur disponibles à <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>partir du.

Dans l’exemple d’application `OnPostConfirmationAsync` ,*(Account/ExternalLogin. cshtml. cs*) établit les revendications de`urn:google:locale`paramètres régionaux ()`urn:google:picture`et d’image () pour `ApplicationUser`le connecté, y compris <xref:System.Security.Claims.ClaimTypes.GivenName>une revendication pour :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification. Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :

* Le navigateur détecte que l’en-tête du cookie est trop long.
* La taille globale de la demande est trop importante.

Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :

* Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.
* Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> authentification des cookies pour stocker l’identité entre les demandes. Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.

## <a name="save-the-access-token"></a>Enregistrer le jeton d’accès

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dans le après une autorisation réussie. `SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.

L’exemple d’application définit la valeur `SaveTokens` de `true` sur <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>dans :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe `ApplicationUser`dans `AuthenticationProperties`le.

L’exemple d’application enregistre le jeton d' `OnPostConfirmationAsync` accès dans (nouvel enregistrement utilisateur `OnGetCallbackAsync` ) et (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Comment ajouter des jetons personnalisés supplémentaires

Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre `SaveTokens`de, l’exemple d’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ajoute un avec <xref:System.DateTime> le actuel pour un `TicketCreated` [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Création et ajout de revendications

L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection. Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Les utilisateurs peuvent définir des actions personnalisées en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> dérivant de et en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> implémentant la méthode abstraite.

Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Suppression des revendications et des actions de revendication

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> le donné de la collection. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.

## <a name="sample-app-output"></a>Exemple de sortie d’application

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

Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter. Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application. Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client. Pour plus d’informations, consultez <xref:security/authentication/social/index>. L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Définir l’ID client et la clé secrète client

Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client. Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur. Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur. Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Google](xref:security/authentication/google-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs d’authentification](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Établir l’étendue de l’authentification

Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>le. Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.

| Fournisseur  | Étendue                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Dans l’exemple d’application, la `userinfo.profile` portée de Google est automatiquement ajoutée par le <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> Framework quand est appelé <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>sur le. Si l’application requiert des étendues supplémentaires, ajoutez-les aux options. Dans l’exemple suivant, l’étendue `https://www.googleapis.com/auth/user.birthday.read` Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapper des clés de données utilisateur et créer des revendications

Dans les options du fournisseur, spécifiez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> ou pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion. Pour plus d’informations sur les types de <xref:System.Security.Claims.ClaimTypes>revendication, consultez.

L’exemple d’application crée des revendications`urn:google:locale`de paramètres régionaux (`urn:google:picture`) et d’image `locale` ( `picture` ) à partir des clés et dans les données utilisateur de Google :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker des `ApplicationUser` revendications pour les données utilisateur disponibles à <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>partir du.

Dans l’exemple d’application `OnPostConfirmationAsync` ,*(Account/ExternalLogin. cshtml. cs*) établit les revendications de`urn:google:locale`paramètres régionaux ()`urn:google:picture`et d’image () pour `ApplicationUser`le connecté, y compris <xref:System.Security.Claims.ClaimTypes.GivenName>une revendication pour :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Par défaut, les revendications d’un utilisateur sont stockées dans le cookie d’authentification. Si le cookie d’authentification est trop volumineux, l’application risque d’échouer en raison des éléments suivants :

* Le navigateur détecte que l’en-tête du cookie est trop long.
* La taille globale de la demande est trop importante.

Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :

* Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.
* Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les intergiciels (middleware) d' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> authentification des cookies pour stocker l’identité entre les demandes. Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.

## <a name="save-the-access-token"></a>Enregistrer le jeton d’accès

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>définit si les jetons d’accès et d’actualisation doivent être stockés <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dans le après une autorisation réussie. `SaveTokens`a la valeur `false` par défaut pour réduire la taille du cookie d’authentification final.

L’exemple d’application définit la valeur `SaveTokens` de `true` sur <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>dans :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe `ApplicationUser`dans `AuthenticationProperties`le.

L’exemple d’application enregistre le jeton d' `OnPostConfirmationAsync` accès dans (nouvel enregistrement utilisateur `OnGetCallbackAsync` ) et (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Comment ajouter des jetons personnalisés supplémentaires

Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre `SaveTokens`de, l’exemple d’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ajoute un avec <xref:System.DateTime> le actuel pour un `TicketCreated` [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Création et ajout de revendications

L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection. Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Les utilisateurs peuvent définir des actions personnalisées en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> dérivant de et en <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> implémentant la méthode abstraite.

Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Suppression des revendications et des actions de revendication

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> le donné de la collection. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.

## <a name="sample-app-output"></a>Exemple de sortie d’application

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

## <a name="additional-resources"></a>Ressources supplémentaires

* &ndash; [application SocialSample d’ingénierie dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) l’exemple d’application liée se trouve sur la branche d' `master` ingénierie [dotnet/AspNetCore GitHub référentiel](https://github.com/dotnet/AspNetCore) . La `master` branche contient du code sous développement actif pour la prochaine version de ASP.net core. Pour afficher une version de l’exemple d’application pour une version finale de ASP.NET Core, utilisez la liste déroulante **branche** pour sélectionner une branche de version `release/{X.Y}`(par exemple).
