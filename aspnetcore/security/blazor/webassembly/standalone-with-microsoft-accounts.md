---
title: Sécuriser une Blazor application autonome webassembly ASP.net core avec des comptes Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 58fd7e6caaffa4c2a293cdddef80def66b8e0680
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138572"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sécuriser une Blazor application autonome webassembly ASP.net core avec des comptes Microsoft

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer une Blazor application webassembly autonome qui utilise [des comptes Microsoft avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification :

1. [Créer un locataire AAD et une application Web](/azure/active-directory/develop/v2-overview)

   Inscrire une application AAD dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :

   1 \. Fournissez un **nom** pour l’application (par exemple, ** Blazor client AAD**).<br>
   2 \. Dans **types de comptes pris en charge**, sélectionnez **comptes dans n’importe quel annuaire d’organisation**.<br>
   3 \. Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.<br>
   4 \. Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .<br>
   5 \. Sélectionnez **Inscription**.

   Dans le**site Web****configurations** > de la plateforme **d’authentification** > :

   1 \. Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.<br>
   2 \. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.<br>
   3 \. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.<br>
   4 \. Sélectionnez le bouton **Enregistrer**.

   Enregistrez l’ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,).

1. Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :

* Connectez-vous à l’application à l’aide d’un compte Microsoft.
* Demandez des jetons d’accès pour les API Microsoft à l’aide de la Blazor même approche que pour les applications autonomes, à condition que vous ayez correctement configuré l’application. Pour plus d’informations, consultez [démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes professionnels ou scolaires`SingleOrg`(), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.

## <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package. Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous inscrivez l’application.

La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/common",
        "ClientId": "{CLIENT ID}"
    }
}
```

Exemple :

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/common",
        "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
    }
}
```

## <a name="access-token-scopes"></a>Étendues de jeton d’accès

Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée. Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par `MsalProviderOptions`défaut du :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte. Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Fournissez l’URI d’étendue sans le schéma et l’hôte :
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Attacher des jetons aux demandes sortantes](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/additional-scenarios>
* [Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
