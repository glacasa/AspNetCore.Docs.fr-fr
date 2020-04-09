---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Microsoft Accounts
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977078"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Microsoft Accounts

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer Blazor une application autonome WebAssembly qui utilise [microsoft Accounts avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification :

1. [Créer un locataire et une application web AAD](/azure/active-directory/develop/v2-overview)

   Enregistrez une application AAD dans la zone**d’enregistrement de l’application** **Azure Active Directory** > du portail Azure :

   1\. Fournir un **nom** pour l’application (par exemple, ** Blazor Client AAD**).<br>
   2\. Dans **les types de compte pris en**charge , sélectionnez des comptes dans **n’importe quel répertoire organisationnel.**<br>
   3\. Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .<br>
   4\. Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**<br>
   5\. Sélectionnez **Inscription**.

   Dans les > **configurations de plate-forme** **d’authentification** > **Web**:

   1\. Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.<br>
   2\. Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.<br>
   3\. Les autres défauts de paiement de l’application sont acceptables pour cette expérience.<br>
   4\. Sélectionnez le bouton **Enregistrer**.

   Enregistrez l’ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`).

1. Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

Après avoir créé l’application, vous devriez être en mesure de:

* Connectez-vous à l’application à l’aide d’un compte Microsoft.
* Demandez des jetons d’accès pour les API Blazor Microsoft en utilisant la même approche que pour les applications autonomes à condition que vous ayez configuré l’application correctement. Pour plus d’informations, voir [Quickstart: Configurer une application pour exposer les API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application. Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous enregistrez l’application.

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
* [Quickstart : Enregistrez une application avec la plate-forme d’identité Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Quickstart: Configurer une application pour exposer les API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
