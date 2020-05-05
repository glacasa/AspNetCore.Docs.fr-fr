---
title: Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0fb4f4176f214d6bf0c005838a0ccbe4487243f2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767972"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer une Blazor application webassembly autonome qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification :

1. Suivez les instructions des rubriques suivantes pour créer un locataire et inscrire une application Web dans le portail Azure :

   * [Créez un AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; enregistrement de locataire avec les informations suivantes :

     1 \. AAD B2C instance (par exemple, `https://contoso.b2clogin.com/`, qui comprend la barre oblique finale)<br>
     2 \. AAD B2C domaine de locataire (par exemple `contoso.onmicrosoft.com`,)

   * [Inscrire une application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web effectuez les sélections suivantes lors de l’inscription de l’application :

     1 \. Définissez **application Web/API Web** sur **Oui**.<br>
     2 \. Définissez **autoriser le Flow implicite** sur **Oui**.<br>
     3 \. Ajoutez une **URL** de réponse `https://localhost:5001/authentication/login-callback`de.

     Enregistrez l’ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,).

   * [Créer des flux](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; d’utilisateurs créez un flux d’utilisateur d’inscription et de connexion.

     Au minimum, sélectionnez l’attribut utilisateur des **revendications** > d’application**nom complet** pour remplir `context.User.Identity.Name` le dans `LoginDisplay` le composant (*Shared/LoginDisplay. Razor*).

     Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple `B2C_1_signupsignin`,).

1. Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

## <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser un compte B2C individuel (`IndividualB2C`), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.

## <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package. Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous inscrivez l’application.

La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Exemple :

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
