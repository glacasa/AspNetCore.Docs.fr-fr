---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976992"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer Blazor une application autonome WebAssembly qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :

[Créer un locataire et une application web AAD](/azure/active-directory/develop/v2-overview):

Enregistrez une application AAD dans la zone**d’enregistrement de l’application** **Azure Active Directory** > du portail Azure :

1. Fournir un **nom** pour l’application (par exemple, ** Blazor Client AAD**).
1. Choisissez un **type de compte pris en charge**. Vous pouvez sélectionner **des comptes dans cet annuaire organisationnel uniquement** pour cette expérience.
1. Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .
1. Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**
1. Sélectionnez **Inscription**.

Dans les > **configurations de plate-forme** **d’authentification** > **Web**:

1. Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.
1. Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.
1. Les autres défauts de paiement de l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Enregistrez les informations suivantes :

* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)
* Id d’annuaire (Id locataire) `22222222-2222-2222-2222-222222222222`(par exemple, )

Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

## <a name="authentication-package"></a>Forfait d’authentification

Lorsqu’une application est créée pour`SingleOrg`utiliser les comptes de travail ou d’école`Microsoft.Authentication.WebAssembly.Msal`(), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (). Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.

Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.

Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.

## <a name="authentication-service-support"></a>Support de service d’authentification

La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet. Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application. Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.

## <a name="access-token-scopes"></a>Portée symbolique d’accès

Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée. Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez la `MsalProviderOptions`portée aux portées symboliques d’accès par défaut des :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Fichier d’importations

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* <xref:security/authentication/azure-active-directory/index>
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
