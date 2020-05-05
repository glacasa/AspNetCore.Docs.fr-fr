---
title: Sécuriser une Blazor application autonome webassembly ASP.net core à l’aide de la bibliothèque d’authentification
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768050"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Sécuriser une Blazor application autonome webassembly ASP.net core à l’aide de la bibliothèque d’authentification

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les instructions de cette rubrique. Consultez les rubriques AAD et AAD B2C dans ce nœud de table des matières.*

Pour créer une Blazor application webassembly autonome qui `Microsoft.AspNetCore.Components.WebAssembly.Authentication` utilise une bibliothèque, exécutez la commande suivante dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au Individual
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

Dans Visual Studio, [créez une Blazor application webassembly](xref:blazor/get-started). Définissez **l’authentification** sur **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** .

## <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes d’utilisateur individuels, l’application reçoit automatiquement une référence de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package pour le package dans le fichier projet de l’application. Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

## <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge de l’authentification des utilisateurs est inscrite `AddOidcAuthentication` dans le conteneur de service `Microsoft.AspNetCore.Components.WebAssembly.Authentication` avec la méthode d’extension fournie par le package. Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

La prise en charge de l’authentification pour les applications autonomes est proposée à l’aide d’Open ID Connect (OIDC). La `AddOidcAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application à l’aide de OIDC. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de l’adresse IP conforme à OIDC. Obtenez les valeurs lors de l’inscription de l’application, qui se produit généralement dans son portail en ligne.

## <a name="access-token-scopes"></a>Étendues de jeton d’accès

Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée. Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jetons par défaut `OidcProviderOptions`du :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Attacher des jetons aux demandes sortantes](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

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
