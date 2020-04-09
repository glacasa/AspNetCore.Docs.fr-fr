---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977052"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer Blazor une application autonome WebAssembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification :

1. Suivez les conseils dans les sujets suivants pour créer un locataire et enregistrer une application web dans le portail Azure :

   * Créez un enregistrement de locataire &ndash; [AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) des informations suivantes :

     1\. AAD B2C instance (par exemple, , `https://contoso.b2clogin.com/`qui comprend la barre oblique de fuite)<br>
     2\. Domaine AAD B2C Tenant `contoso.onmicrosoft.com`(par exemple, )

   * [Enregistrez une application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web Faites les sélections suivantes lors de l’inscription de l’application :

     1\. Définissez **l’application Web / API Web** à **Oui**.<br>
     2\. Définir **Permettre le flux implicite** à **Oui**.<br>
     3\. Ajouter une URL `https://localhost:5001/authentication/login-callback`de **réponse** de .

     Enregistrez l’ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`).

   * [Créer des flux d’utilisateurs](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Créer un flux d’identifiants et d’inscription.

     Au minimum, sélectionnez **l’attribut** > utilisateur**de l’utilisateur Display Name** des revendications d’application pour remplir le `context.User.Identity.Name` `LoginDisplay` composant *(Shared/LoginDisplay.razor*).

     Enregistrez le nom de flux d’utilisateur d’inscription et `B2C_1_signupsignin`d’inscription créé pour l’application (par exemple, ).

1. Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

## <a name="authentication-package"></a>Forfait d’authentification

Lorsqu’une application est créée pour utiliser`IndividualB2C`un compte B2C individuel ( ), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque Microsoft Authentication](/azure/active-directory/develop/msal-overview) ().`Microsoft.Authentication.WebAssembly.Msal` Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.

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
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
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
* <xref:security/authentication/azure-ad-b2c>
* [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
