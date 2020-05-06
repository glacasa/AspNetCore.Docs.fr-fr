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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="ef351-103">Partager des cookies d’authentification entre des applications ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ef351-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="ef351-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ef351-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ef351-105">Les sites Web sont souvent constitués d’applications Web individuelles qui fonctionnent ensemble.</span><span class="sxs-lookup"><span data-stu-id="ef351-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="ef351-106">Pour fournir une expérience d’authentification unique (SSO), les applications Web d’un site doivent partager des cookies d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ef351-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="ef351-107">Pour prendre en charge ce scénario, la pile de protection des données permet de partager l’authentification par cookie Katana et ASP.NET Core tickets d’authentification de cookie.</span><span class="sxs-lookup"><span data-stu-id="ef351-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="ef351-108">Dans les exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="ef351-108">In the examples that follow:</span></span>

* <span data-ttu-id="ef351-109">Le nom du cookie d’authentification est défini sur une valeur `.AspNet.SharedCookie`courante de.</span><span class="sxs-lookup"><span data-stu-id="ef351-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="ef351-110">`AuthenticationType` Est défini sur `Identity.Application` explicitement ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="ef351-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="ef351-111">Un nom d’application commun est utilisé pour permettre au système de protection des données de partager des`SharedCookieApp`clés de protection des données ().</span><span class="sxs-lookup"><span data-stu-id="ef351-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="ef351-112">`Identity.Application`est utilisé comme schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ef351-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="ef351-113">Quel que soit le schéma utilisé, il doit être utilisé de manière cohérente *dans et dans* les applications de cookies partagées comme schéma par défaut ou en le définissant explicitement.</span><span class="sxs-lookup"><span data-stu-id="ef351-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="ef351-114">Le schéma est utilisé lors du chiffrement et du déchiffrement des cookies. un schéma cohérent doit donc être utilisé entre les applications.</span><span class="sxs-lookup"><span data-stu-id="ef351-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="ef351-115">Un emplacement de stockage de [clé de protection des données](xref:security/data-protection/implementation/key-management) commun est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ef351-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="ef351-116">Dans ASP.NET Core Apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> est utilisé pour définir l’emplacement de stockage de la clé.</span><span class="sxs-lookup"><span data-stu-id="ef351-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="ef351-117">Dans .NET Framework Apps, l’intergiciel (middleware) d’authentification par <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>cookie utilise une implémentation de.</span><span class="sxs-lookup"><span data-stu-id="ef351-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="ef351-118">`DataProtectionProvider`fournit des services de protection des données pour le chiffrement et le déchiffrement des données de charge utile du cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ef351-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="ef351-119">L' `DataProtectionProvider` instance est isolée du système de protection des données utilisé par d’autres parties de l’application.</span><span class="sxs-lookup"><span data-stu-id="ef351-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="ef351-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo,\<Action IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepte <xref:System.IO.DirectoryInfo> un pour spécifier l’emplacement du stockage de la clé de protection des données.</span><span class="sxs-lookup"><span data-stu-id="ef351-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="ef351-121">`DataProtectionProvider`requiert le package NuGet [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="ef351-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="ef351-122">Dans ASP.NET Core applications 2. x, référencez le [AspNetCore](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ef351-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="ef351-123">Dans .NET Framework applications, ajoutez une référence de package à [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="ef351-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="ef351-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>définit le nom d’application courant.</span><span class="sxs-lookup"><span data-stu-id="ef351-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="ef351-125">Partager des cookies d’authentification avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="ef351-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="ef351-126">Quand vous utilisez IdentityASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="ef351-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="ef351-127">Les clés de protection des données et le nom de l’application doivent être partagés entre les applications.</span><span class="sxs-lookup"><span data-stu-id="ef351-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="ef351-128">Un emplacement de stockage de clés commun est fourni <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> à la méthode dans les exemples suivants.</span><span class="sxs-lookup"><span data-stu-id="ef351-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="ef351-129">Utilisez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> pour configurer un nom d’application partagée commun`SharedCookieApp` (dans les exemples suivants).</span><span class="sxs-lookup"><span data-stu-id="ef351-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="ef351-130">Pour plus d’informations, consultez <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="ef351-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="ef351-131">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> méthode d’extension pour configurer le service de protection des données pour les cookies.</span><span class="sxs-lookup"><span data-stu-id="ef351-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="ef351-132">Le type d’authentification par `Identity.Application`défaut est.</span><span class="sxs-lookup"><span data-stu-id="ef351-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="ef351-133">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ef351-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="ef351-134">Partager des cookies d’authentification sans ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="ef351-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="ef351-135">Lorsque vous utilisez des cookies directement Identitysans ASP.net Core, configurez la `Startup.ConfigureServices`protection et l’authentification des données dans.</span><span class="sxs-lookup"><span data-stu-id="ef351-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ef351-136">Dans l’exemple suivant, le type d’authentification est défini `Identity.Application`sur :</span><span class="sxs-lookup"><span data-stu-id="ef351-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="ef351-137">Partager des cookies entre différents chemins d’accès de base</span><span class="sxs-lookup"><span data-stu-id="ef351-137">Share cookies across different base paths</span></span>

<span data-ttu-id="ef351-138">Un cookie d’authentification utilise [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) comme cookie. [path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)par défaut.</span><span class="sxs-lookup"><span data-stu-id="ef351-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="ef351-139">Si le cookie de l’application doit être partagé entre différents chemins de `Path` base, doit être substitué :</span><span class="sxs-lookup"><span data-stu-id="ef351-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="ef351-140">Partager des cookies entre des sous-domaines</span><span class="sxs-lookup"><span data-stu-id="ef351-140">Share cookies across subdomains</span></span>

<span data-ttu-id="ef351-141">Lorsque vous hébergez des applications qui partagent des cookies entre des sous-domaines, spécifiez un domaine commun dans la propriété [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="ef351-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="ef351-142">Pour partager des cookies entre les `contoso.com`applications à, `first_subdomain.contoso.com` tels `second_subdomain.contoso.com`que et, `Cookie.Domain` spécifiez le en tant que `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="ef351-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="ef351-143">Chiffrer les clés de protection des données au repos</span><span class="sxs-lookup"><span data-stu-id="ef351-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="ef351-144">Pour les déploiements de production, `DataProtectionProvider` configurez pour chiffrer les clés au repos avec DPAPI ou un certificat X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="ef351-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="ef351-145">Pour plus d’informations, consultez <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="ef351-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="ef351-146">Dans l’exemple suivant, une empreinte numérique de certificat est <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>fournie à :</span><span class="sxs-lookup"><span data-stu-id="ef351-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="ef351-147">Partager des cookies d’authentification entre ASP.NET 4. x et les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef351-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="ef351-148">Les applications ASP.NET 4. x qui utilisent l’intergiciel (middleware) d’authentification de cookie Katana peuvent être configurées pour générer des cookies d’authentification compatibles avec l’intergiciel (middleware) d’authentification de cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ef351-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="ef351-149">Cela permet de mettre à niveau les applications individuelles d’un site de grande taille en plusieurs étapes, tout en fournissant une authentification unique fluide sur le site.</span><span class="sxs-lookup"><span data-stu-id="ef351-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="ef351-150">Quand une application utilise l’intergiciel Katana cookie Authentication, elle `UseCookieAuthentication` appelle dans le fichier *Startup.auth.cs* du projet.</span><span class="sxs-lookup"><span data-stu-id="ef351-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="ef351-151">Les projets d’application Web ASP.NET 4. x créés avec Visual Studio 2013 et versions ultérieures utilisent l’intergiciel (middleware) d’authentification de cookie Katana par défaut.</span><span class="sxs-lookup"><span data-stu-id="ef351-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="ef351-152">Bien `UseCookieAuthentication` que soit obsolète et non prise en charge pour les applications `UseCookieAuthentication` ASP.net Core, l’appel de dans une application ASP.net 4. x qui utilise l’intergiciel (middleware) d’authentification de cookie Katana est valide.</span><span class="sxs-lookup"><span data-stu-id="ef351-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="ef351-153">Une application ASP.NET 4. x doit cibler .NET Framework 4.5.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ef351-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="ef351-154">Dans le cas contraire, l’installation des packages NuGet nécessaires échoue.</span><span class="sxs-lookup"><span data-stu-id="ef351-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="ef351-155">Pour partager des cookies d’authentification entre une application ASP.NET 4. x et une application ASP.NET Core, configurez l’application ASP.NET Core comme indiqué dans la section [partager des cookies d’authentification parmi les applications ASP.net Core](#share-authentication-cookies-with-aspnet-core-identity) , puis configurez l’application ASP.net 4. x comme suit.</span><span class="sxs-lookup"><span data-stu-id="ef351-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="ef351-156">Vérifiez que les packages de l’application sont mis à jour avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="ef351-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="ef351-157">Installez le package [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) dans chaque application ASP.net 4. x.</span><span class="sxs-lookup"><span data-stu-id="ef351-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="ef351-158">Recherchez et modifiez l’appel à `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ef351-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="ef351-159">Modifiez le nom du cookie pour qu’il corresponde au nom utilisé par l’intergiciel (`.AspNet.SharedCookie` middleware) d’authentification de cookie ASP.net Core (dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="ef351-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="ef351-160">Dans l’exemple suivant, le type d’authentification est défini `Identity.Application`sur.</span><span class="sxs-lookup"><span data-stu-id="ef351-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="ef351-161">Fournissez une instance d' `DataProtectionProvider` un initialisé à l’emplacement de stockage de la clé de protection des données commune.</span><span class="sxs-lookup"><span data-stu-id="ef351-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="ef351-162">Confirmez que le nom de l’application est défini sur le nom d’application commun utilisé par toutes les applications`SharedCookieApp` qui partagent des cookies d’authentification (dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="ef351-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="ef351-163">Si vous ne `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` définissez `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`pas et <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> , définissez sur une revendication qui distingue les utilisateurs uniques.</span><span class="sxs-lookup"><span data-stu-id="ef351-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="ef351-164">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="ef351-164">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="ef351-165">Lors de la génération d’une identité d’utilisateur,`Identity.Application`le type d’authentification () doit `AuthenticationType` correspondre au `UseCookieAuthentication` type défini dans l’ensemble avec dans *App_Start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="ef351-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="ef351-166">*Models/IdentityModels. cs*:</span><span class="sxs-lookup"><span data-stu-id="ef351-166">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="ef351-167">Utiliser une base de données utilisateur commune</span><span class="sxs-lookup"><span data-stu-id="ef351-167">Use a common user database</span></span>

<span data-ttu-id="ef351-168">Lorsque les applications utilisent le Identity même schéma (même version Identityde), vérifiez que Identity le système de chaque application pointe vers la même base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ef351-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="ef351-169">Dans le cas contraire, le système d’identité génère des erreurs au moment de l’exécution lorsqu’il tente de faire correspondre les informations contenues dans le cookie d’authentification avec les informations contenues dans sa base de données.</span><span class="sxs-lookup"><span data-stu-id="ef351-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="ef351-170">Lorsque le Identity schéma est différent parmi les applications, généralement parce que les applications Identity utilisent des versions différentes, le partage d’une base de données Identity commune basée sur la dernière version de n’est pas possible sans Identity remappage et ajout de colonnes dans les schémas d’autres applications.</span><span class="sxs-lookup"><span data-stu-id="ef351-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="ef351-171">Il est souvent plus efficace de mettre à niveau les autres applications pour utiliser Identity la dernière version afin qu’une base de données commune puisse être partagée par les applications.</span><span class="sxs-lookup"><span data-stu-id="ef351-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ef351-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ef351-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
