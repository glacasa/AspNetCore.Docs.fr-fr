---
title: Configuration de connexion externe de compte Microsoft avec ASP.NET Core
author: rick-anderson
description: Cet exemple démontre l’intégration de l’authentification de l’utilisateur du compte Microsoft dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277247"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuration de connexion externe de compte Microsoft avec ASP.NET Core

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet exemple vous montre comment permettre aux utilisateurs de se connecter avec leur compte Microsoft à l’aide du projet ASP.NET Core 3.0 créé sur la [page précédente](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Créez l’application dans microsoft Developer Portal

* Ajoutez le package [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet au projet.
* Naviguez vers le portail Azure - Page [d’enregistrement de l’application](https://go.microsoft.com/fwlink/?linkid=2083908) et créez ou connectez-vous à un compte Microsoft :

Si vous n’avez pas de compte Microsoft, sélectionnez **Créer un**. Après la signature, vous êtes redirigé vers la page **d’inscriptions App** :

* Sélectionner **Nouvelle inscription**
* Entrez un **nom**.
* Sélectionnez une option pour **les types de compte pris en charge**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* Sous **Redirect URI**, entrez votre URL de développement avec `/signin-microsoft` joint. Par exemple : `https://localhost:5001/signin-microsoft`. Le système d’authentification Microsoft configuré `/signin-microsoft` plus tard dans cet échantillon traitera automatiquement les demandes en route pour implémenter le flux OAuth.
* Sélect **Registre**

### <a name="create-client-secret"></a>Créer un secret client

* Dans le volet gauche, sélectionnez **Certificats et secrets**.
* Sous **les secrets de client**, sélectionnez Nouveau secret **client**

  * Ajoutez une description pour le secret du client.
  * Sélectionnez le bouton **Ajouter.**

* Sous **les secrets de client,** copiez la valeur du secret du client.

Le segment `/signin-microsoft` URI est défini comme le rappel par défaut du fournisseur d’authentification Microsoft. Vous pouvez modifier la callback par défaut URI tout en configurant le middleware d’authentification Microsoft via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Stockez l’IDENTIFIANT et le secret du client Microsoft

Stockez des paramètres sensibles tels que l’IDENTIFIANT du client Microsoft et des valeurs secrètes avec [Secret Manager](xref:security/app-secrets). Pour cet échantillon, utilisez les étapes suivantes :

1. Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).
1. Stockez les réglages sensibles dans `Authentication:Microsoft:ClientId` le `Authentication:Microsoft:ClientSecret`magasin secret local avec les clés secrètes et :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurer l’authentification du compte Microsoft

Ajoutez le service de `Startup.ConfigureServices`compte Microsoft au :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Pour plus d’informations sur les options de configuration prises en charge par l’authentification de compte Microsoft, consultez la référence [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API. Cela peut être utilisé pour demander des informations différentes sur l’utilisateur.

## <a name="sign-in-with-microsoft-account"></a>Connectez-vous au compte Microsoft

Exécutez l’application et cliquez sur **Connectez-vous**. Une option de connexion avec Microsoft apparaît. Lorsque vous cliquez sur Microsoft, vous êtes redirigé vers Microsoft pour l’authentification. Après s’être inscrit avec votre compte Microsoft, vous serez invité à laisser l’application accéder à vos informations :

Appuyez **oui** et vous serez redirigé vers le site Web où vous pouvez définir votre e-mail.

Vous êtes maintenant connecté à l’aide de vos informations d’identification Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Dépannage

* Si le fournisseur de compte Microsoft vous redirige vers une page d’erreur de `#` signe, notez le titre d’erreur et les paramètres de la chaîne de requête de description directement après le (hashtag) dans l’Uri.

  Bien que le message d’erreur semble indiquer un problème avec l’authentification Microsoft, la cause la plus fréquente est votre application Uri ne correspond pas à l’un des **URIs Redirect** spécifiés pour la plate-forme **Web.**
* Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*. Le modèle de projet utilisé dans cet échantillon garantit que cela est fait.
* Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.* Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article a montré comment vous pouvez authentifier avec Microsoft. Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).

* Une fois que vous publiez votre site Web sur l’application Web Azure, créez un nouveau secret client dans le portail microsoft Developer.

* Définissez `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` les paramètres et les paramètres d’application dans le portail Azure. Le système de configuration est configuré pour lire les clés des variables de l’environnement.
