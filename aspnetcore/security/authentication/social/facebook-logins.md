---
title: Configuration de connexion externe Facebook dans ASP.NET Core
author: rick-anderson
description: Tutorial avec des exemples de code démontrant l’intégration de l’authentification de l’utilisateur de compte Facebook dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277299"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuration de connexion externe Facebook dans ASP.NET Core

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Ce tutoriel avec des exemples de code montre comment permettre à vos utilisateurs de se connecter avec leur compte Facebook à l’aide d’un échantillon ASP.NET projet Core 3.0 créé sur la [page précédente](xref:security/authentication/social/index). Nous commençons par créer un IDENTIFIANT d’application Facebook en suivant les [étapes officielles](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Créer l’application dans Facebook

* Ajoutez le package [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet au projet.

* Naviguez vers la page [de l’application Facebook Developers](https://developers.facebook.com/apps/) et connectez-vous. Si vous n’avez pas déjà de compte Facebook, utilisez le **lien Facebook** sur la page de connexion pour en créer un.  Une fois que vous avez un compte Facebook, suivez les instructions pour vous inscrire en tant que développeur Facebook.

* Du menu **My Apps,** sélectionnez **Create App** pour créer un nouvel IDENTIFIANT d’application.

   ![Facebook pour le portail des développeurs ouvert dans Microsoft Edge](index/_static/FBMyApps.png)

* Remplissez le formulaire et appuyez sur le bouton **Create App ID.**

  ![Créer un nouveau formulaire d’identification d’application](index/_static/FBNewAppId.png)

* Sur la nouvelle carte App, sélectionnez **Ajouter un produit**.  Sur la carte **Facebook Login,** cliquez **sur Set Up** 

  ![Page d’installation de produits](index/_static/FBProductSetup.png)

* **L’assistant Quickstart** lance avec **Choisissez une plate-forme** comme première page. Déroucher l’assistant pour l’instant en cliquant sur le lien **Paramètres** **de connexion FaceBook** dans le menu en bas à gauche:

  ![Passer le démarrage rapide](index/_static/FBSkipQuickStart.png)

* Vous êtes présenté avec la page **Paramètres OAuth Client** :

  ![Page Paramètres OAuth Client](index/_static/FBOAuthSetup.png)

* Entrez votre URI de développement avec */signin-facebook* joint dans le champ valide `https://localhost:44320/signin-facebook` **OAuth Redirect URIs** (par exemple: ). L’authentification Facebook configurée plus tard dans ce tutoriel traitera automatiquement les demandes à *l’itinéraire /signin-facebook* pour implémenter le flux OAuth.

> [!NOTE]
> L’URI */signin-facebook* est défini comme le rappel par défaut du fournisseur d’authentification Facebook. Vous pouvez modifier le rappel par défaut URI tout en configurant le middleware d’authentification Facebook via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Cliquez sur **Enregistrer les modifications**.

* Cliquez sur **Paramètres** > **Lien de base** dans la navigation gauche.

  Sur cette page, prenez `App ID` note `App Secret`de votre et de votre . Vous ajouterez les deux dans votre application ASP.NET Core dans la section suivante :

* Lors du déploiement du site, vous devez revoir la page d’installation **Facebook Login** et enregistrer un nouvel URI public.

## <a name="store-the-facebook-app-id-and-secret"></a>Stockez l’ID de l’application Facebook et le secret

Stockez des paramètres sensibles tels que l’ID de l’application Facebook et des valeurs secrètes avec [Secret Manager](xref:security/app-secrets). Pour cet échantillon, utilisez les étapes suivantes :

1. Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).
1. Stockez les réglages sensibles dans `Authentication:Facebook:AppId` le `Authentication:Facebook:AppSecret`magasin secret local avec les clés secrètes et :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurer Facebook Authentification

Ajouter le service `ConfigureServices` Facebook dans la méthode dans le fichier *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Connectez-vous à Facebook

* Exécutez l’application et **sélectionnez Log in**. 
* **Utilisez un autre service pour vous connecter.**, sélectionnez Facebook.
* Vous êtes redirigé vers **Facebook** pour l’authentification.
* Entrez vos informations d’identification Facebook.
* Vous êtes redirigé vers votre site où vous pouvez définir votre e-mail.

Vous êtes maintenant connecté à l’aide de vos informations d’identification Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Réagir pour annuler autoriser l’inscription externe

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>peut fournir un chemin de réorientation vers l’agent utilisateur lorsque l’utilisateur n’approuve pas la demande d’autorisation demandée.

Le code suivant `AccessDeniedPath` `"/AccessDeniedPathInfo"`définit le :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Nous recommandons `AccessDeniedPath` que la page contienne les informations suivantes :

*  L’authentification à distance a été annulée.
* Cette application nécessite une authentification.
* Pour essayer de nouveau vous connecter, sélectionnez le lien Login.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* Naviguez vers [facebook.com](https://www.facebook.com/)
* Si vous êtes connecté, vous devez vous déconnecter.
* Exécutez l’application et sélectionnez l’inscription Facebook.
* Sélectionnez **Pas maintenant**. Vous êtes redirigé `AccessDeniedPath` vers la page spécifiée.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consultez la référence [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API pour plus d’informations sur les options de configuration prises en charge par l’authentification De Facebook. Les options de configuration peuvent être utilisées pour :

* Demandez des informations différentes sur l’utilisateur.
* Ajoutez des arguments de chaîne de requête pour personnaliser l’expérience de connexion.

## <a name="troubleshooting"></a>Dépannage

* **ASP.NET Core 2.x seulement:** Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*. Le modèle de projet utilisé dans ce tutoriel garantit que cela est fait.
* Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtenez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.* Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez authentifier avec Facebook. Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).

* Une fois que vous publiez votre site Web `AppSecret` sur l’application Web Azure, vous devez réinitialiser le portail des développeurs Facebook.

* Définissez `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` les paramètres et les paramètres d’application dans le portail Azure. Le système de configuration est configuré pour lire les clés des variables de l’environnement.
