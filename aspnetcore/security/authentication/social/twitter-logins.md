---
title: Configuration de connexion externe Twitter avec ASP.NET Core
author: rick-anderson
description: Ce tutoriel démontre l’intégration de l’authentification des utilisateurs de compte Twitter dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277286"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuration de connexion externe Twitter avec ASP.NET Core

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet exemple montre comment permettre aux utilisateurs de [se connecter avec leur compte Twitter à](https://dev.twitter.com/web/sign-in/desktop-browser) l’aide d’un échantillon ASP.NET projet Core 3.0 créé sur la page [précédente](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Créer l’application sur Twitter

* Ajoutez le package [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet au projet.

* Naviguez [https://apps.twitter.com/](https://apps.twitter.com/) et connectez-vous. Si vous n’avez pas déjà de compte Twitter, utilisez le lien **[Inscrivez-vous maintenant](https://twitter.com/signup)** pour en créer un.

* Sélectionnez **Créer une application**. Remplissez le **nom de l’application,** la **description de l’application** et **le site Web** public URI (cela peut être temporaire jusqu’à ce que vous enregistrez le nom de domaine) :

* Cochez la case à côté pour **activer connectez-vous avec Twitter**

* Microsoft.AspNetCore.Identity exige des utilisateurs d’avoir une adresse e-mail par défaut. Allez à l’onglet **Autorisations,** cliquez sur le bouton **Modifier** et cochez la case à côté **de l’adresse e-mail request des utilisateurs**.

* Entrez votre URI de développement avec `/signin-twitter` joint dans le champ **d’URL De rappel** (par exemple : `https://webapp128.azurewebsites.net/signin-twitter`). Le système d’authentification Twitter configuré `/signin-twitter` plus tard dans cet échantillon traitera automatiquement les demandes en cours d’implémentation du flux OAuth.

  > [!NOTE]
  > Le segment `/signin-twitter` URI est défini comme le rappel par défaut du fournisseur d’authentification Twitter. Vous pouvez modifier le rappel par défaut URI tout en configurant le middleware d’authentification Twitter via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Remplissez le reste du formulaire et sélectionnez **Créer**. Les nouveaux détails de l’application sont affichés :

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Stockez la clé API grand public De Twitter et le secret

Stockez des paramètres sensibles tels que la clé API grand public De Twitter et secret avec [Secret Manager](xref:security/app-secrets). Pour cet échantillon, utilisez les étapes suivantes :

1. Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).
1. Stockez les réglages sensibles dans `Authentication:Twitter:ConsumerKey` le `Authentication:Twitter:ConsumerSecret`magasin secret local avec les clés de secrets et :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Ces jetons peuvent être trouvés sur l’onglet **Keys and Access Tokens** après la création d’une nouvelle application Twitter :

## <a name="configure-twitter-authentication"></a>Configurer l’authentification Twitter

Ajouter le service `ConfigureServices` Twitter dans la méthode dans *Startup.cs* fichier:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consultez la référence [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API pour plus d’informations sur les options de configuration prises en charge par l’authentification Twitter. Cela peut être utilisé pour demander des informations différentes sur l’utilisateur.

## <a name="sign-in-with-twitter"></a>Connectez-vous avec Twitter

Exécutez l’application et **sélectionnez Log in**. Une option de connexion avec Twitter apparaît:

En cliquant sur **Twitter** redirige vers Twitter pour l’authentification:

Après avoir entré vos informations d’identification Twitter, vous êtes redirigé vers le site Web où vous pouvez définir votre e-mail.

Vous êtes maintenant connecté à l’aide de vos informations d’identification Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Dépannage

* **ASP.NET Core 2.x seulement:** Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*. Le modèle de projet utilisé dans cet échantillon garantit que cela est fait.
* Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.* Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez authentifier avec Twitter. Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).

* Une fois que vous publiez votre site Web `ConsumerSecret` sur l’application Web Azure, vous devez réinitialiser le portail des développeurs Twitter.

* Définissez `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` les paramètres et les paramètres d’application dans le portail Azure. Le système de configuration est configuré pour lire les clés des variables de l’environnement.
