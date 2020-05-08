---
title: Partager des cookies d’authentification entre des applications ASP.NET
author: rick-anderson
description: Découvrez comment partager des cookies d’authentification parmi les applications ASP.NET 4. x et ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776140"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Partager des cookies d’authentification entre des applications ASP.NET

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Les sites Web sont souvent constitués d’applications Web individuelles qui fonctionnent ensemble. Pour fournir une expérience d’authentification unique (SSO), les applications Web d’un site doivent partager des cookies d’authentification. Pour prendre en charge ce scénario, la pile de protection des données permet de partager l’authentification par cookie Katana et ASP.NET Core tickets d’authentification de cookie.

Dans les exemples suivants :

* Le nom du cookie d’authentification est défini sur une valeur `.AspNet.SharedCookie`courante de.
* `AuthenticationType` Est défini sur `Identity.Application` explicitement ou par défaut.
* Un nom d’application commun est utilisé pour permettre au système de protection des données de partager des`SharedCookieApp`clés de protection des données ().
* `Identity.Application`est utilisé comme schéma d’authentification. Quel que soit le schéma utilisé, il doit être utilisé de manière cohérente *dans et dans* les applications de cookies partagées comme schéma par défaut ou en le définissant explicitement. Le schéma est utilisé lors du chiffrement et du déchiffrement des cookies. un schéma cohérent doit donc être utilisé entre les applications.
* Un emplacement de stockage de [clé de protection des données](xref:security/data-protection/implementation/key-management) commun est utilisé.
  * Dans ASP.NET Core Apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> est utilisé pour définir l’emplacement de stockage de la clé.
  * Dans .NET Framework Apps, l’intergiciel (middleware) d’authentification par <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>cookie utilise une implémentation de. `DataProtectionProvider`fournit des services de protection des données pour le chiffrement et le déchiffrement des données de charge utile du cookie d’authentification. L' `DataProtectionProvider` instance est isolée du système de protection des données utilisé par d’autres parties de l’application. [DataProtectionProvider. Create (System. IO. DirectoryInfo,\<Action IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepte <xref:System.IO.DirectoryInfo> un pour spécifier l’emplacement du stockage de la clé de protection des données.
* `DataProtectionProvider`requiert le package NuGet [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * Dans ASP.NET Core applications 2. x, référencez le [AspNetCore](xref:fundamentals/metapackage-app).
  * Dans .NET Framework applications, ajoutez une référence de package à [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>définit le nom d’application courant.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Partager des cookies d’authentification avec ASP.NET CoreIdentity

Quand vous utilisez IdentityASP.net Core :

* Les clés de protection des données et le nom de l’application doivent être partagés entre les applications. Un emplacement de stockage de clés commun est fourni <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> à la méthode dans les exemples suivants. Utilisez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> pour configurer un nom d’application partagée commun`SharedCookieApp` (dans les exemples suivants). Pour plus d'informations, consultez <xref:security/data-protection/configuration/overview>.
* Utilisez la <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> méthode d’extension pour configurer le service de protection des données pour les cookies.
* Le type d’authentification par `Identity.Application`défaut est.

Dans `Startup.ConfigureServices` :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Partager des cookies d’authentification sans ASP.NET CoreIdentity

Lorsque vous utilisez des cookies directement Identitysans ASP.net Core, configurez la `Startup.ConfigureServices`protection et l’authentification des données dans. Dans l’exemple suivant, le type d’authentification est défini `Identity.Application`sur :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>Partager des cookies entre différents chemins d’accès de base

Un cookie d’authentification utilise [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) comme cookie. [path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)par défaut. Si le cookie de l’application doit être partagé entre différents chemins de `Path` base, doit être substitué :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Partager des cookies entre des sous-domaines

Lorsque vous hébergez des applications qui partagent des cookies entre des sous-domaines, spécifiez un domaine commun dans la propriété [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Pour partager des cookies entre les `contoso.com`applications à, `first_subdomain.contoso.com` tels `second_subdomain.contoso.com`que et, `Cookie.Domain` spécifiez le en tant que `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Chiffrer les clés de protection des données au repos

Pour les déploiements de production, `DataProtectionProvider` configurez pour chiffrer les clés au repos avec DPAPI ou un certificat X509Certificate. Pour plus d'informations, consultez <xref:security/data-protection/implementation/key-encryption-at-rest>. Dans l’exemple suivant, une empreinte numérique de certificat est <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>fournie à :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Partager des cookies d’authentification entre ASP.NET 4. x et les applications ASP.NET Core

Les applications ASP.NET 4. x qui utilisent l’intergiciel (middleware) d’authentification de cookie Katana peuvent être configurées pour générer des cookies d’authentification compatibles avec l’intergiciel (middleware) d’authentification de cookie ASP.NET Core. Cela permet de mettre à niveau les applications individuelles d’un site de grande taille en plusieurs étapes, tout en fournissant une authentification unique fluide sur le site.

Quand une application utilise l’intergiciel Katana cookie Authentication, elle `UseCookieAuthentication` appelle dans le fichier *Startup.auth.cs* du projet. Les projets d’application Web ASP.NET 4. x créés avec Visual Studio 2013 et versions ultérieures utilisent l’intergiciel (middleware) d’authentification de cookie Katana par défaut. Bien `UseCookieAuthentication` que soit obsolète et non prise en charge pour les applications `UseCookieAuthentication` ASP.net Core, l’appel de dans une application ASP.net 4. x qui utilise l’intergiciel (middleware) d’authentification de cookie Katana est valide.

Une application ASP.NET 4. x doit cibler .NET Framework 4.5.1 ou version ultérieure. Dans le cas contraire, l’installation des packages NuGet nécessaires échoue.

Pour partager des cookies d’authentification entre une application ASP.NET 4. x et une application ASP.NET Core, configurez l’application ASP.NET Core comme indiqué dans la section [partager des cookies d’authentification parmi les applications ASP.net Core](#share-authentication-cookies-with-aspnet-core-identity) , puis configurez l’application ASP.net 4. x comme suit.

Vérifiez que les packages de l’application sont mis à jour avec les versions les plus récentes. Installez le package [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) dans chaque application ASP.net 4. x.

Recherchez et modifiez l’appel à `UseCookieAuthentication`:

* Modifiez le nom du cookie pour qu’il corresponde au nom utilisé par l’intergiciel (`.AspNet.SharedCookie` middleware) d’authentification de cookie ASP.net Core (dans l’exemple).
* Dans l’exemple suivant, le type d’authentification est défini `Identity.Application`sur.
* Fournissez une instance d' `DataProtectionProvider` un initialisé à l’emplacement de stockage de la clé de protection des données commune.
* Confirmez que le nom de l’application est défini sur le nom d’application commun utilisé par toutes les applications`SharedCookieApp` qui partagent des cookies d’authentification (dans l’exemple).

Si vous ne `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` définissez `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`pas et <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> , définissez sur une revendication qui distingue les utilisateurs uniques.

*App_Start/Startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Lors de la génération d’une identité d’utilisateur,`Identity.Application`le type d’authentification () doit `AuthenticationType` correspondre au `UseCookieAuthentication` type défini dans l’ensemble avec dans *App_Start/Startup.auth.cs*.

*Models/IdentityModels. cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Utiliser une base de données utilisateur commune

Lorsque les applications utilisent le Identity même schéma (même version Identityde), vérifiez que Identity le système de chaque application pointe vers la même base de données utilisateur. Dans le cas contraire, le système d’identité génère des erreurs au moment de l’exécution lorsqu’il tente de faire correspondre les informations contenues dans le cookie d’authentification avec les informations contenues dans sa base de données.

Lorsque le Identity schéma est différent parmi les applications, généralement parce que les applications Identity utilisent des versions différentes, le partage d’une base de données Identity commune basée sur la dernière version de n’est pas possible sans Identity remappage et ajout de colonnes dans les schémas d’autres applications. Il est souvent plus efficace de mettre à niveau les autres applications pour utiliser Identity la dernière version afin qu’une base de données commune puisse être partagée par les applications.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/web-farm>
