---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec la bibliothèque Authentication
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977039"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec la bibliothèque Authentication

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les orientations dans ce sujet. Voir les sujets AAD et AAD B2C dans ce tableau de nœud de contenu.*

Pour créer Blazor une application autonome WebAssembly qui utilise `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la bibliothèque, exécutez la commande suivante dans une coque de commande :

```dotnetcli
dotnet new blazorwasm -au Individual
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

Dans Visual Studio, [créez une Blazor application WebAssembly](xref:blazor/get-started). Définissez **l’authentification** **des comptes utilisateur individuels** avec l’option Des comptes **d’utilisateurs Store.**

## <a name="authentication-package"></a>Forfait d’authentification

Lorsqu’une application est créée pour utiliser des comptes utilisateur `Microsoft.AspNetCore.Components.WebAssembly.Authentication` individuels, l’application reçoit automatiquement une référence de paquet pour le paquet dans le fichier de projet de l’application. Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.

Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.

## <a name="authentication-service-support"></a>Support de service d’authentification

La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddOidcAuthentication` méthode d’extension fournie par le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet. Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Le support d’authentification pour les applications autonomes est offert à l’aide d’Open ID Connect (OIDC). La `AddOidcAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application à l’aide d’OIDC. Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la propriété intellectuelle conforme à l’OIDC. Obtenez les valeurs lorsque vous enregistrez l’application, qui se produit généralement dans leur portail en ligne.

## <a name="access-token-scopes"></a>Portée symbolique d’accès

Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée. Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez `OidcProviderOptions`la portée aux portées symboliques par défaut de la :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte. Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Fournir la portée URI sans le régime et l’hôte:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Fichier d’importations

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Composant de l’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
