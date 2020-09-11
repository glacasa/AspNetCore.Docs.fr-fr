---
title: Multi-Factor Authentication dans ASP.NET Core
author: damienbod
description: Découvrez comment configurer Multi-Factor Authentication (MFA) dans une application ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: e224f947335ea8ea6ed8887dfadb52202bfd7866
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009503"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="1c660-103">Multi-Factor Authentication dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c660-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="1c660-104">Par [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="1c660-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="1c660-105">Multi-Factor Authentication (MFA) est un processus dans lequel un utilisateur est demandé pendant un événement de connexion pour des formes d’identification supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="1c660-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="1c660-106">Vous pouvez entrer un code à partir d’un téléphone portable, utiliser une clé FIDO2 ou fournir une analyse par empreinte digitale.</span><span class="sxs-lookup"><span data-stu-id="1c660-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="1c660-107">Lorsque vous avez besoin d’une deuxième forme d’authentification, la sécurité est améliorée.</span><span class="sxs-lookup"><span data-stu-id="1c660-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="1c660-108">Le facteur supplémentaire n’est pas facilement obtenu ou dupliqué par une personne malveillante.</span><span class="sxs-lookup"><span data-stu-id="1c660-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="1c660-109">Cet article aborde les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="1c660-109">This article covers the following areas:</span></span>

* <span data-ttu-id="1c660-110">Qu’est-ce que MFA et quels flux MFA sont recommandés</span><span class="sxs-lookup"><span data-stu-id="1c660-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="1c660-111">Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1c660-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="1c660-112">Envoyer une exigence de connexion MFA au serveur OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="1c660-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="1c660-113">Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA</span><span class="sxs-lookup"><span data-stu-id="1c660-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="1c660-114">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="1c660-114">MFA, 2FA</span></span>

<span data-ttu-id="1c660-115">L’authentification multifacteur nécessite au moins deux types de preuves pour une identité comme un nom que vous connaissez, un nom que vous possédez ou une validation biométrique pour l’utilisateur à authentifier.</span><span class="sxs-lookup"><span data-stu-id="1c660-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="1c660-116">L’authentification à deux facteurs (2FA) est similaire à un sous-ensemble de l’authentification MFA, mais la différence est que l’authentification multifacteur peut nécessiter au moins deux facteurs pour prouver l’identité.</span><span class="sxs-lookup"><span data-stu-id="1c660-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="1c660-117">MFA TOTP (algorithme de mot de passe à usage unique basé sur le temps)</span><span class="sxs-lookup"><span data-stu-id="1c660-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="1c660-118">L’authentification MFA avec TOTP est une implémentation prise en charge à l’aide de ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1c660-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="1c660-119">Cela peut être utilisé avec n’importe quelle application d’authentificateur conforme, y compris :</span><span class="sxs-lookup"><span data-stu-id="1c660-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="1c660-120">Application Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="1c660-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="1c660-121">Application Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="1c660-121">Google Authenticator App</span></span>

<span data-ttu-id="1c660-122">Pour plus d’informations sur l’implémentation, consultez le lien suivant :</span><span class="sxs-lookup"><span data-stu-id="1c660-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="1c660-123">Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c660-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="1c660-124">MFA FIDO2 ou mot de passe</span><span class="sxs-lookup"><span data-stu-id="1c660-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="1c660-125">FIDO2 est actuellement :</span><span class="sxs-lookup"><span data-stu-id="1c660-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="1c660-126">Le moyen le plus sûr d’obtenir l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="1c660-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="1c660-127">Le seul circuit MFA qui protège contre les attaques par hameçonnage.</span><span class="sxs-lookup"><span data-stu-id="1c660-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="1c660-128">À l’heure actuelle, ASP.NET Core ne prend pas en charge directement FIDO2.</span><span class="sxs-lookup"><span data-stu-id="1c660-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="1c660-129">FIDO2 peut être utilisé pour l’authentification MFA ou les flux avec mot de passe.</span><span class="sxs-lookup"><span data-stu-id="1c660-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="1c660-130">Azure Active Directory prend en charge les FIDO2 et les flux avec mot de passe.</span><span class="sxs-lookup"><span data-stu-id="1c660-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="1c660-131">Pour plus d’informations, consultez [options d’authentification par mot de passe pour Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="1c660-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="1c660-132">SMS MFA</span><span class="sxs-lookup"><span data-stu-id="1c660-132">MFA SMS</span></span>

<span data-ttu-id="1c660-133">L’authentification MFA avec SMS augmente la sécurité massivement comparée à l’authentification par mot de passe (facteur unique).</span><span class="sxs-lookup"><span data-stu-id="1c660-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="1c660-134">Toutefois, l’utilisation de SMS comme second facteur n’est plus recommandée.</span><span class="sxs-lookup"><span data-stu-id="1c660-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="1c660-135">Un trop grand nombre de vecteurs d’attaque connus existent pour ce type d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="1c660-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="1c660-136">Instructions du NIST</span><span class="sxs-lookup"><span data-stu-id="1c660-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="1c660-137">Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1c660-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="1c660-138">L’authentification multifacteur peut être forcée sur les utilisateurs qui accèdent à des pages sensibles au sein d’une ASP.NET Core Identity application.</span><span class="sxs-lookup"><span data-stu-id="1c660-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="1c660-139">Cela peut être utile pour les applications où différents niveaux d’accès existent pour les différentes identités.</span><span class="sxs-lookup"><span data-stu-id="1c660-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="1c660-140">Par exemple, les utilisateurs peuvent être en mesure d’afficher les données de profil à l’aide d’une connexion de mot de passe, mais un administrateur doit utiliser l’authentification multifacteur pour accéder aux pages d’administration.</span><span class="sxs-lookup"><span data-stu-id="1c660-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="1c660-141">Étendre la connexion avec une revendication MFA</span><span class="sxs-lookup"><span data-stu-id="1c660-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="1c660-142">Le code de démonstration est configuré à l’aide de ASP.NET Core avec les Identity Razor pages et.</span><span class="sxs-lookup"><span data-stu-id="1c660-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="1c660-143">La `AddIdentity` méthode est utilisée au lieu d' `AddDefaultIdentity` une, donc une `IUserClaimsPrincipalFactory` implémentation peut être utilisée pour ajouter des revendications à l’identité après une connexion réussie.</span><span class="sxs-lookup"><span data-stu-id="1c660-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="1c660-144">La `AdditionalUserClaimsPrincipalFactory` classe ajoute la `amr` revendication aux revendications de l’utilisateur uniquement après une connexion réussie.</span><span class="sxs-lookup"><span data-stu-id="1c660-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="1c660-145">La valeur de la revendication est lue à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c660-145">The claim's value is read from the database.</span></span> <span data-ttu-id="1c660-146">La revendication est ajoutée ici, car l’utilisateur ne doit accéder qu’à l’affichage protégé le plus élevé si l’identité s’est connectée avec MFA.</span><span class="sxs-lookup"><span data-stu-id="1c660-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="1c660-147">Si la vue de base de données est lue directement à partir de la base de données au lieu d’utiliser la revendication, il est possible d’accéder à la vue sans MFA directement après l’activation de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="1c660-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="1c660-148">Étant donné que l' Identity installation du service a changé dans la `Startup` classe, les dispositions du Identity doivent être mises à jour.</span><span class="sxs-lookup"><span data-stu-id="1c660-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="1c660-149">Générez l’échafaudage des Identity pages dans l’application.</span><span class="sxs-lookup"><span data-stu-id="1c660-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="1c660-150">Définissez la disposition dans le fichier \* Identity /Account/Manage/_Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="1c660-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="1c660-151">Affectez également la disposition de toutes les pages de gestion à partir des Identity pages :</span><span class="sxs-lookup"><span data-stu-id="1c660-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="1c660-152">Valider les conditions d’authentification multifacteur dans la page d’administration</span><span class="sxs-lookup"><span data-stu-id="1c660-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="1c660-153">La Razor page d’administration vérifie que l’utilisateur s’est connecté à l’aide de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="1c660-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="1c660-154">Dans la `OnGet` méthode, l’identité est utilisée pour accéder aux revendications de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c660-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="1c660-155">La `amr` valeur est vérifiée pour la revendication `mfa` .</span><span class="sxs-lookup"><span data-stu-id="1c660-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="1c660-156">Si cette revendication ne figure pas dans l’identité ou si a la valeur `false` , la page est redirigée vers la page activer mfa.</span><span class="sxs-lookup"><span data-stu-id="1c660-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="1c660-157">Cela est possible parce que l’utilisateur s’est déjà connecté, mais sans MFA.</span><span class="sxs-lookup"><span data-stu-id="1c660-157">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="1c660-158">Logique d’interface utilisateur pour activer/désactiver les informations de connexion de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c660-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="1c660-159">Une stratégie d’autorisation a été ajoutée au démarrage.</span><span class="sxs-lookup"><span data-stu-id="1c660-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="1c660-160">La stratégie requiert la `amr` revendication avec la valeur `mfa` .</span><span class="sxs-lookup"><span data-stu-id="1c660-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="1c660-161">Cette stratégie peut ensuite être utilisée dans la `_Layout` vue pour afficher ou masquer le menu d' **administration** avec l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="1c660-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="1c660-162">Si l’identité s’est connectée à l’aide de l’authentification multifacteur, le menu **admin** s’affiche sans l’avertissement d’info-bulle.</span><span class="sxs-lookup"><span data-stu-id="1c660-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="1c660-163">Lorsque l’utilisateur s’est connecté sans MFA, le menu **admin (non activé)** s’affiche avec l’info-bulle qui informe l’utilisateur (ce qui explique l’avertissement).</span><span class="sxs-lookup"><span data-stu-id="1c660-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="1c660-164">Si l’utilisateur se connecte sans MFA, l’avertissement s’affiche :</span><span class="sxs-lookup"><span data-stu-id="1c660-164">If the user logs in without MFA, the warning is displayed:</span></span>

![Authentification MFA administrateur](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="1c660-166">L’utilisateur est redirigé vers la vue d’activation de l’authentification MFA quand vous cliquez sur le lien d' **administration** :</span><span class="sxs-lookup"><span data-stu-id="1c660-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![L’administrateur Active l’authentification MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="1c660-168">Envoyer une exigence de connexion MFA au serveur OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="1c660-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="1c660-169">Le `acr_values` paramètre peut être utilisé pour transmettre la `mfa` valeur requise du client au serveur dans une demande d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c660-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="1c660-170">Le `acr_values` paramètre doit être géré sur le serveur OpenID Connect pour que cela fonctionne.</span><span class="sxs-lookup"><span data-stu-id="1c660-170">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="1c660-171">OpenID Connect ASP.NET Core client</span><span class="sxs-lookup"><span data-stu-id="1c660-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="1c660-172">ASP.NET Core l' Razor application cliente OpenID pages Connect utilise la `AddOpenIdConnect` méthode pour se connecter au serveur OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="1c660-172">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="1c660-173">Le `acr_values` paramètre est défini avec la `mfa` valeur et est envoyé avec la demande d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c660-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="1c660-174">`OpenIdConnectEvents`Est utilisé pour ajouter ce.</span><span class="sxs-lookup"><span data-stu-id="1c660-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="1c660-175">Pour connaître les valeurs de paramètre recommandées `acr_values` , consultez valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="1c660-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="1c660-176">Exemple OpenID Connect Identity Server 4 Server avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1c660-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="1c660-177">Sur le serveur OpenID Connect, qui est implémenté à l’aide ASP.NET Core Identity de avec les vues MVC, une nouvelle vue nommée *ErrorEnable2FA. cshtml* est créée.</span><span class="sxs-lookup"><span data-stu-id="1c660-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="1c660-178">La vue :</span><span class="sxs-lookup"><span data-stu-id="1c660-178">The view:</span></span>

* <span data-ttu-id="1c660-179">Indique si le Identity provient d’une application qui requiert l’authentification MFA, mais que l’utilisateur n’a pas activé cette fonctionnalité dans Identity .</span><span class="sxs-lookup"><span data-stu-id="1c660-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="1c660-180">Informe l’utilisateur et ajoute un lien pour activer cette.</span><span class="sxs-lookup"><span data-stu-id="1c660-180">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="1c660-181">Dans la `Login` méthode, l' `IIdentityServerInteractionService` implémentation de l’interface `_interaction` est utilisée pour accéder aux paramètres de la demande de connexion OpenID.</span><span class="sxs-lookup"><span data-stu-id="1c660-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="1c660-182">Le `acr_values` paramètre est accessible à l’aide de la `AcrValues` propriété.</span><span class="sxs-lookup"><span data-stu-id="1c660-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="1c660-183">À mesure que le client l' `mfa` a envoyé avec set, cette valeur peut ensuite être vérifiée.</span><span class="sxs-lookup"><span data-stu-id="1c660-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="1c660-184">Si l’authentification multifacteur est requise et que l’authentification MFA est activée pour l’utilisateur dans ASP.NET Core Identity , la connexion se poursuit.</span><span class="sxs-lookup"><span data-stu-id="1c660-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="1c660-185">Lorsque l’option MFA n’est pas activée pour l’utilisateur, l’utilisateur est redirigé vers la vue personnalisée *ErrorEnable2FA. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c660-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="1c660-186">ASP.NET Core IdentityConnecte ensuite l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c660-186">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="1c660-187">La `ExternalLoginCallback` méthode fonctionne comme la Identity connexion locale.</span><span class="sxs-lookup"><span data-stu-id="1c660-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="1c660-188">La `AcrValues` valeur est recherchée dans la propriété `mfa` .</span><span class="sxs-lookup"><span data-stu-id="1c660-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="1c660-189">Si la `mfa` valeur est présente, l’authentification MFA est forcée avant la fin de la connexion (par exemple, redirigé vers la `ErrorEnable2FA` vue).</span><span class="sxs-lookup"><span data-stu-id="1c660-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="1c660-190">Si l’utilisateur est déjà connecté, l’application cliente :</span><span class="sxs-lookup"><span data-stu-id="1c660-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="1c660-191">Valide toujours la `amr` revendication.</span><span class="sxs-lookup"><span data-stu-id="1c660-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="1c660-192">Peut configurer l’authentification multifacteur avec un lien vers la ASP.NET Core Identity vue.</span><span class="sxs-lookup"><span data-stu-id="1c660-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="1c660-194">Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA</span><span class="sxs-lookup"><span data-stu-id="1c660-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="1c660-195">Cet exemple montre comment une Razor application de Page ASP.net Core, qui utilise OpenID Connect pour se connecter, peut exiger que les utilisateurs soient authentifiés à l’aide de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="1c660-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="1c660-196">Pour valider l’exigence d’authentification multifacteur, une `IAuthorizationRequirement` spécification est créée.</span><span class="sxs-lookup"><span data-stu-id="1c660-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="1c660-197">Il sera ajouté aux pages à l’aide d’une stratégie qui requiert l’authentification MFA.</span><span class="sxs-lookup"><span data-stu-id="1c660-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="1c660-198">Un `AuthorizationHandler` est implémenté qui utilisera la `amr` revendication et vérifiera la valeur `mfa` .</span><span class="sxs-lookup"><span data-stu-id="1c660-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="1c660-199">`amr`Est retourné dans le `id_token` d’une authentification réussie et peut avoir de nombreuses valeurs différentes comme défini dans la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="1c660-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="1c660-200">La valeur retournée dépend de la manière dont l’identité a été authentifiée et de l’implémentation du serveur OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="1c660-200">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="1c660-201">Le `AuthorizationHandler` utilise la `RequireMfa` spécification et valide la `amr` revendication.</span><span class="sxs-lookup"><span data-stu-id="1c660-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="1c660-202">Le serveur OpenID Connect peut être implémenté à l’aide Identity de 4 avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1c660-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="1c660-203">Lorsqu’un utilisateur se connecte à l’aide de TOTP, la `amr` revendication est retournée avec une valeur mfa.</span><span class="sxs-lookup"><span data-stu-id="1c660-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="1c660-204">Si vous utilisez une autre implémentation de serveur OpenID Connect ou un autre type d’authentification multifacteur, la `amr` revendication peut avoir une valeur différente.</span><span class="sxs-lookup"><span data-stu-id="1c660-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="1c660-205">Le code doit être étendu pour accepter cela également.</span><span class="sxs-lookup"><span data-stu-id="1c660-205">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="1c660-206">Dans la `Startup.ConfigureServices` méthode, la `AddOpenIdConnect` méthode est utilisée comme schéma de stimulation par défaut.</span><span class="sxs-lookup"><span data-stu-id="1c660-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="1c660-207">Le gestionnaire d’autorisations, qui est utilisé pour vérifier la `amr` revendication, est ajouté à l’inversion du conteneur de contrôle.</span><span class="sxs-lookup"><span data-stu-id="1c660-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="1c660-208">Une stratégie est ensuite créée, ce qui ajoute la `RequireMfa` spécification.</span><span class="sxs-lookup"><span data-stu-id="1c660-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="1c660-209">Cette stratégie est ensuite utilisée dans la Razor page, selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="1c660-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="1c660-210">La stratégie peut également être ajoutée globalement pour l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="1c660-210">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="1c660-211">Si l’utilisateur s’authentifie sans MFA, la `amr` revendication aura probablement une `pwd` valeur.</span><span class="sxs-lookup"><span data-stu-id="1c660-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="1c660-212">La demande n’est pas autorisée à accéder à la page.</span><span class="sxs-lookup"><span data-stu-id="1c660-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="1c660-213">À l’aide des valeurs par défaut, l’utilisateur est redirigé vers la page *compte/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="1c660-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="1c660-214">Ce comportement peut être modifié ou vous pouvez implémenter votre propre logique personnalisée ici.</span><span class="sxs-lookup"><span data-stu-id="1c660-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="1c660-215">Dans cet exemple, un lien est ajouté afin que l’utilisateur valide puisse configurer MFA pour son compte.</span><span class="sxs-lookup"><span data-stu-id="1c660-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="1c660-216">Désormais, seuls les utilisateurs qui s’authentifient avec MFA peuvent accéder à la page ou au site Web.</span><span class="sxs-lookup"><span data-stu-id="1c660-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="1c660-217">Si différents types d’authentification multifacteur sont utilisés ou si 2FA est OK, la `amr` revendication aura des valeurs différentes et doit être traitée correctement.</span><span class="sxs-lookup"><span data-stu-id="1c660-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="1c660-218">Différents serveurs OpenID Connect retournent également des valeurs différentes pour cette revendication et peuvent ne pas suivre la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="1c660-218">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="1c660-219">Lors de la connexion sans authentification multifacteur (par exemple, à l’aide d’un mot de passe uniquement) :</span><span class="sxs-lookup"><span data-stu-id="1c660-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="1c660-220">`amr`A la `pwd` valeur :</span><span class="sxs-lookup"><span data-stu-id="1c660-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="1c660-222">Accès refusé :</span><span class="sxs-lookup"><span data-stu-id="1c660-222">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="1c660-224">Vous pouvez également ouvrir une session avec un mot de passe à usage unique avec Identity :</span><span class="sxs-lookup"><span data-stu-id="1c660-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="1c660-226">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c660-226">Additional resources</span></span>

* [<span data-ttu-id="1c660-227">Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c660-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="1c660-228">Options d’authentification sans mot de passe pour Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1c660-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="1c660-229">FIDO2 bibliothèque .NET pour l’attestation et l’assertion FIDO2/webauthn à l’aide de .NET</span><span class="sxs-lookup"><span data-stu-id="1c660-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="1c660-230">Authentification Isard</span><span class="sxs-lookup"><span data-stu-id="1c660-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
