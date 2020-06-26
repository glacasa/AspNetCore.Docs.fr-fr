---
title: Configurer ASP.NET CoreIdentity
author: AdrienTorris
description: Comprenez ASP.NET Core Identity valeurs par défaut et apprenez à configurer des Identity Propriétés pour utiliser des valeurs personnalisées.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408966"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="8eb60-103">Configurer ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="8eb60-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="8eb60-104">ASP.NET Core Identity utilise les valeurs par défaut pour les paramètres tels que la stratégie de mot de passe, le verrouillage et la configuration des cookies.</span><span class="sxs-lookup"><span data-stu-id="8eb60-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="8eb60-105">Ces paramètres peuvent être remplacés dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="8eb60-106">Options</span><span class="sxs-lookup"><span data-stu-id="8eb60-106"> options</span></span>

<span data-ttu-id="8eb60-107">La classe [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) représente les options qui peuvent être utilisées pour configurer le Identity système.</span><span class="sxs-lookup"><span data-stu-id="8eb60-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="8eb60-108">`IdentityOptions`doit être défini **après** l’appel `AddIdentity` de ou de `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="8eb60-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="8eb60-109">LégitimitéIdentity</span><span class="sxs-lookup"><span data-stu-id="8eb60-109">Claims Identity</span></span>

<span data-ttu-id="8eb60-110">[IdentityOptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) spécifie le [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) avec les propriétés indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="8eb60-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="8eb60-111">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-111">Property</span></span> | <span data-ttu-id="8eb60-112">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-112">Description</span></span> | <span data-ttu-id="8eb60-113">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="8eb60-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="8eb60-115">Obtient ou définit le type de revendication utilisé pour une revendication de rôle.</span><span class="sxs-lookup"><span data-stu-id="8eb60-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="8eb60-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="8eb60-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="8eb60-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="8eb60-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="8eb60-118">Obtient ou définit le type de revendication utilisé pour la revendication de cachet de sécurité.</span><span class="sxs-lookup"><span data-stu-id="8eb60-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="8eb60-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="8eb60-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="8eb60-120">Obtient ou définit le type de revendication utilisé pour la revendication de l’identificateur d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8eb60-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="8eb60-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="8eb60-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="8eb60-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="8eb60-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="8eb60-123">Obtient ou définit le type de revendication utilisé pour la revendication de nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8eb60-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="8eb60-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="8eb60-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="8eb60-125">Verrouillage.</span><span class="sxs-lookup"><span data-stu-id="8eb60-125">Lockout</span></span>

<span data-ttu-id="8eb60-126">Le verrouillage est défini dans la méthode [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="8eb60-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="8eb60-127">Le code précédent est basé sur le `Login` Identity modèle.</span><span class="sxs-lookup"><span data-stu-id="8eb60-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="8eb60-128">Les options de verrouillage sont définies dans `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8eb60-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="8eb60-129">Le code précédent définit le [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) IdentityOptions avec les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="8eb60-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="8eb60-130">Une authentification réussie réinitialise le nombre d’échecs de tentatives d’accès et réinitialise l’horloge.</span><span class="sxs-lookup"><span data-stu-id="8eb60-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="8eb60-131">[IdentityOptions. lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) spécifie le [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) avec les propriétés indiquées dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="8eb60-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="8eb60-132">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-132">Property</span></span> | <span data-ttu-id="8eb60-133">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-133">Description</span></span> | <span data-ttu-id="8eb60-134">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="8eb60-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="8eb60-136">Détermine si un nouvel utilisateur peut être verrouillé.</span><span class="sxs-lookup"><span data-stu-id="8eb60-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="8eb60-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="8eb60-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="8eb60-138">Durée pendant laquelle un utilisateur est verrouillé lorsqu’un verrouillage se produit.</span><span class="sxs-lookup"><span data-stu-id="8eb60-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="8eb60-139">5 minutes</span><span class="sxs-lookup"><span data-stu-id="8eb60-139">5 minutes</span></span> |
| [<span data-ttu-id="8eb60-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="8eb60-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="8eb60-141">Nombre d’échecs de tentative d’accès jusqu’à ce qu’un utilisateur soit verrouillé, si le verrouillage est activé.</span><span class="sxs-lookup"><span data-stu-id="8eb60-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="8eb60-142">5</span><span class="sxs-lookup"><span data-stu-id="8eb60-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="8eb60-143">Mot de passe</span><span class="sxs-lookup"><span data-stu-id="8eb60-143">Password</span></span>

<span data-ttu-id="8eb60-144">Par défaut, Identity exige que les mots de passe contiennent un caractère majuscule, un caractère minuscule, un chiffre et un caractère non alphanumérique.</span><span class="sxs-lookup"><span data-stu-id="8eb60-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="8eb60-145">Les mots de passe doivent comporter au moins six caractères.</span><span class="sxs-lookup"><span data-stu-id="8eb60-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="8eb60-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) peut être défini dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8eb60-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="8eb60-147">[IdentityOptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) spécifie le [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) avec les propriétés indiquées dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="8eb60-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="8eb60-148">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-148">Property</span></span> | <span data-ttu-id="8eb60-149">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-149">Description</span></span> | <span data-ttu-id="8eb60-150">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="8eb60-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="8eb60-152">Requiert un nombre compris entre 0-9 dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="8eb60-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="8eb60-154">Longueur minimale du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-154">The minimum length of the password.</span></span> | <span data-ttu-id="8eb60-155">6</span><span class="sxs-lookup"><span data-stu-id="8eb60-155">6</span></span> |
| [<span data-ttu-id="8eb60-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="8eb60-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="8eb60-157">Requiert un caractère minuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="8eb60-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="8eb60-159">Requiert un caractère non alphanumérique dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="8eb60-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="8eb60-161">S’applique uniquement à ASP.NET Core 2,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="8eb60-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="8eb60-162">Requiert le nombre de caractères distincts dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="8eb60-163">1</span><span class="sxs-lookup"><span data-stu-id="8eb60-163">1</span></span> |
| [<span data-ttu-id="8eb60-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="8eb60-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="8eb60-165">Requiert un caractère majuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="8eb60-166">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-166">Property</span></span> | <span data-ttu-id="8eb60-167">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-167">Description</span></span> | <span data-ttu-id="8eb60-168">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="8eb60-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="8eb60-170">Requiert un nombre compris entre 0-9 dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="8eb60-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="8eb60-172">Longueur minimale du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-172">The minimum length of the password.</span></span> | <span data-ttu-id="8eb60-173">6</span><span class="sxs-lookup"><span data-stu-id="8eb60-173">6</span></span> |
| [<span data-ttu-id="8eb60-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="8eb60-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="8eb60-175">Requiert un caractère minuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="8eb60-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="8eb60-177">Requiert un caractère non alphanumérique dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="8eb60-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="8eb60-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="8eb60-179">Requiert un caractère majuscule dans le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="8eb60-180">Connexion</span><span class="sxs-lookup"><span data-stu-id="8eb60-180">Sign-in</span></span>

<span data-ttu-id="8eb60-181">Le code suivant définit les `SignIn` paramètres (aux valeurs par défaut) :</span><span class="sxs-lookup"><span data-stu-id="8eb60-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="8eb60-182">[IdentityOptions. Signy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) spécifie le [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) avec les propriétés indiquées dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="8eb60-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="8eb60-183">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-183">Property</span></span> | <span data-ttu-id="8eb60-184">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-184">Description</span></span> | <span data-ttu-id="8eb60-185">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="8eb60-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="8eb60-187">Requiert un e-mail confirmé pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="8eb60-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="8eb60-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="8eb60-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="8eb60-189">Nécessite un numéro de téléphone confirmé pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="8eb60-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="8eb60-190">Jetons</span><span class="sxs-lookup"><span data-stu-id="8eb60-190">Tokens</span></span>

<span data-ttu-id="8eb60-191">[IdentityOptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) spécifie le [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) avec les propriétés affichées dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="8eb60-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="8eb60-192">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="8eb60-193">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="8eb60-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="8eb60-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="8eb60-195">Obtient ou définit le `AuthenticatorTokenProvider` utilisé pour valider les connexions à deux facteurs avec un authentificateur.</span><span class="sxs-lookup"><span data-stu-id="8eb60-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="8eb60-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="8eb60-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="8eb60-197">Obtient ou définit le `ChangeEmailTokenProvider` utilisé pour générer les jetons utilisés dans les e-mails de confirmation de modification de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="8eb60-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="8eb60-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="8eb60-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="8eb60-199">Obtient ou définit le `ChangePhoneNumberTokenProvider` utilisé pour générer les jetons utilisés lors de la modification des numéros de téléphone.</span><span class="sxs-lookup"><span data-stu-id="8eb60-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="8eb60-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="8eb60-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="8eb60-201">Obtient ou définit le fournisseur de jetons utilisé pour générer les jetons utilisés dans les e-mails de confirmation de compte.</span><span class="sxs-lookup"><span data-stu-id="8eb60-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="8eb60-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="8eb60-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="8eb60-203">Obtient ou définit le [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) utilisé pour générer les jetons utilisés dans les courriers électroniques de réinitialisation de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="8eb60-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="8eb60-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="8eb60-205">Utilisé pour construire un [fournisseur de jetons utilisateur](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) avec la clé utilisée comme nom du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="8eb60-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="8eb60-206">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="8eb60-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="8eb60-207">[IdentityOptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) spécifie l' [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) avec les propriétés affichées dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="8eb60-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="8eb60-208">Propriété</span><span class="sxs-lookup"><span data-stu-id="8eb60-208">Property</span></span> | <span data-ttu-id="8eb60-209">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-209">Description</span></span> | <span data-ttu-id="8eb60-210">Default</span><span class="sxs-lookup"><span data-stu-id="8eb60-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="8eb60-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="8eb60-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="8eb60-212">Caractères autorisés dans le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8eb60-212">Allowed characters in the username.</span></span> | <span data-ttu-id="8eb60-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="8eb60-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="8eb60-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="8eb60-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="8eb60-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="8eb60-215">0123456789</span></span><br><span data-ttu-id="8eb60-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="8eb60-216">-.\_@+</span></span> |
| [<span data-ttu-id="8eb60-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="8eb60-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="8eb60-218">Nécessite que chaque utilisateur dispose d’un e-mail unique.</span><span class="sxs-lookup"><span data-stu-id="8eb60-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="8eb60-219">Paramètres de cookie</span><span class="sxs-lookup"><span data-stu-id="8eb60-219">Cookie settings</span></span>

<span data-ttu-id="8eb60-220">Configurez le cookie de l’application dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8eb60-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8eb60-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) doit être appelé **après** l’appel `AddIdentity` de ou de `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="8eb60-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="8eb60-222">Pour plus d’informations, consultez [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="8eb60-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="8eb60-223">Options de hachage de mot de passe</span><span class="sxs-lookup"><span data-stu-id="8eb60-223">Password Hasher options</span></span>

<span data-ttu-id="8eb60-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Obtient et définit les options de hachage de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="8eb60-225">Option</span><span class="sxs-lookup"><span data-stu-id="8eb60-225">Option</span></span> | <span data-ttu-id="8eb60-226">Description</span><span class="sxs-lookup"><span data-stu-id="8eb60-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="8eb60-227">Mode de compatibilité utilisé lors du hachage de nouveaux mots de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="8eb60-228">La valeur par défaut est <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="8eb60-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="8eb60-229">Le premier octet d’un mot de passe haché, appelé *marqueur de format*, spécifie la version de l’algorithme de hachage utilisé pour hacher le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="8eb60-230">Lors de la vérification d’un mot de passe par rapport à un hachage, la <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> méthode sélectionne l’algorithme approprié en fonction du premier octet.</span><span class="sxs-lookup"><span data-stu-id="8eb60-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="8eb60-231">Un client est en mesure de s’authentifier, quelle que soit la version de l’algorithme utilisée pour hacher le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="8eb60-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="8eb60-232">La définition du mode de compatibilité affecte le hachage de *nouveaux mots de passe*.</span><span class="sxs-lookup"><span data-stu-id="8eb60-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="8eb60-233">Nombre d’itérations utilisées pour hacher les mots de passe à l’aide de PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="8eb60-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="8eb60-234">Cette valeur est utilisée uniquement lorsque <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> a la valeur <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="8eb60-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="8eb60-235">La valeur doit être un entier positif et la valeur par défaut est `10000` .</span><span class="sxs-lookup"><span data-stu-id="8eb60-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="8eb60-236">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> a la valeur `12000` dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8eb60-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
