---
title: Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4c5fc6a07ce52ee9560cd2aa0c7dbc3032522f00
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138453"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer une Blazor application webassembly autonome qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :

[Créez un client et une application Web AAD](/azure/active-directory/develop/v2-overview):

Inscrire une application AAD dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :

1. Fournissez un **nom** pour l’application (par exemple, ** Blazor client AAD**).
1. Choisissez un **type de compte pris en charge**. Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.
1. Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.
1. Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .
1. Sélectionnez **Inscription**.

Dans le**site Web****configurations** > de la plateforme **d’authentification** > :

1. Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.
1. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Notez les informations suivantes :

* ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)
* ID de répertoire (ID de locataire) (par `22222222-2222-2222-2222-222222222222`exemple,)

Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

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
        "Authority": "https://login.microsoftonline.com/{TENANT ID}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Exemple :

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
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
* <xref:security/authentication/azure-active-directory/index>
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
